---
title: Hive优化
abbrlink: 2cf3653f
date: 2024-09-29 20:25:05
tags:
---

Hive 优化 | MapReduce 优化 | 数据倾斜

<!--more-->

# Hive 优化

## ① Fetch 抓取

Fetch抓取 是指 Hive 中某些情况的查询可以不走 MR 计算

在 hive-default.xml.template 文件中 hive.fetch.task.conversion 默认是more，老版本默认是minimal

该属性[none, minimal, more]修改为more后，在全局查找、字段查找、limit查找等是不会走MR。

```xml
<property>
    <name>hive.fetch.task.conversion</name>
    <value>more</value>
</property>
```

## ② 本地模式

大多数的 Hadoop Job 是需要 Hadoop 提供的完整的可扩展性来处理大数据集的。不过，有时 Hive 的输入数据量较小时，启用集群资源的耗时可能比任务执行本身要更久。故可以采用 Hive本地模式在单台机器上处理任务。对于小数据集，执行时间可以明显缩短。

用户可以设置 MR本地模式 参数，local MR 的最大输入数据量，local MR 的最大输入文件个数等参数进行配置

```SQL
-- MR本地模式
set hive.exec.mode.local.auto=true;

-- 设置local MR的最大输入数据量，当小于此值时采用local MR，默认134217728，128M
set hive.exec.mode.local.auto.inputbytes.max=500000000;

-- 设置local MR的最大输入文件个数，当小于此值时采用local MR，默认为4
set hive.exec.mode.local.auto.input.files.max=10;
```

```sql
-- 项目实测：
-- 开启本地模式
hive > set hive.exec.mode.local.auto=true;
hive > select * from insurance_ods.customers; 
Time taken: 3.597 seconds, Fetched: 1054 row(s)
-- 关闭本地模式
hive > set hive.exec.mode.local.auto=false;
hive > select * from insurance_ods.customers; 
Time taken: 34.03 seconds, Fetched: 1054 row(s)
```

## ③ 表的优化

### Ⅰ：小表 Join 大表

将key相对分散、并且数据量较小的表放在join的左边，这样可以有效减少内存溢出错误发生的几率；再进一步，可以使用Group让小的维度表（1000条以下）先进内存。在Map端完成Reduce。

```SQL
-- 项目实测:
-- 关闭map join参数
set hive.auto.convert.join=false;

-- 小表 Join 大表
insert overwrite table xxxx
select xxx from xxxx smalltable
left join xxxx bigtable
on smalltable.id = bigtable.id;
-- Time taken: 27.43 seconds;

-- 大表 Join 小表
insert overwrite table xxxx
select xxx from xxxx bigtable
left join xxxx smalltable
on bigtable.id = smalltable.id
-- Time taken: 29.18 seconds;
```

### Ⅱ：大表 Join 大表

#### 1 ）空 key 过滤

有些大表某些key对应的数据太多，相同key对应的数据会发给相同的Reduce处理，从而造成少数Reduce效率过慢，数据倾斜，内存不足等问题。可以根据业务数据，分析这些key是否为异常数据，在SQL表关联时将这部分key数据过滤掉。

```sql
-- 项目实测:
-- 测试不过滤空id
insert overwrite table xxxx
select xxxx from t1 left join t2 on t1.id = t2.id;
-- Time taken: 42.038 seconds;

-- 测试过滤空id
insert overwrite table xxxx
select xxxx from (select * from t1 where id is not null) t1 left join t2 on t1.id = t2.id;
-- Time taken: 31.725 seconds;
```

#### 2）空 key 转换

空key过滤用于处理的是key过多且此类数据为无用异常数据的情况，但如果业务数据中存在key过多但均是有效数据的，不得过滤掉此类数据，可以选择为其赋上一个随机的值（加盐），让同key数据随机均分到不同的Reduce上。

```sql
-- 项目实测:
-- 设置5个reduce个数
set mapreduce.job.reduces=5;

-- 不随机分布null值
insert overwrite table xxxx
select xxxx from t1 
	left join t2 on t1.id = t2.id;

-- 随机分布null值
insert overwrite table xxxx
select xxxx from t1 
	left join t2 on (case when t1.id is null then concat('hive', rand()) else t1.id) = t2.id;
```

### Ⅲ：Map Join

笼统的说，Hive中的Join可分为Common Join（Reduce阶段完成join）和Map Join（Map阶段完成join）。

如果不指定MapJoin或者不符合MapJoin的条件，那么Hive解析器会将Join操作转换成Common Join，即在Reduce阶段完成join，容易发生数据倾斜。Common Join整个过程包含Map、Shuffle、Reduce阶段。

可以用MapJoin把小表全部加载到内存在Map端进行Join，避免Reducer处理。

```sql
-- 开启Map Join参数
set hive.auto.convert.join=true;

-- 设置大表小表的阈值(默认25M以下是小表)
set hive.mapjoin.smalltable.filesize=25000000;
```

### Ⅳ：Group By

#### 1) Map端提前聚合

默认情况下，Map阶段相同key的数据分发给一个Reduce，当一个key数据过大时就倾斜了。而并不是所有的聚合操作都需要在Reduce端完成，很多聚合操作可以先在Map端进行部分聚合，最后在Reduce端得出最终结果。

```sql
-- 开启Map端提前聚合参数(默认开启)
set hive.map.aggr=true;

-- 在Map端进行聚合操作的条目数
set hive.groupby.mapaggr.checkinterval=100000;
```

#### 2) 负载均衡

开启负载均衡参数后，任务执行会有两个MR Job。

第一个MR中，Map的输出结果会随机分布到Reduce中，每个Reduce做部分聚合操作并输出结果。这样处理的结果是相同的Group By Key有可能被分发到不同的Reduce中，从而达到负载均衡的目的。

第二个MR再根据预处理的数据结果按照Group By Key分布到Reduce中，这一过程可以保证相同的Group By Key被分布到同一个Reduce中，最后完成最终的聚合操作。

```sql
-- 开启负载均衡参数(默认关闭)
set hive.groupby.skewindata=true;
```

### Ⅴ：count(distinct) 去重统计

count(distinct)操作只会调用一个Reduce Task来完成，所以当数据量过大时，其处理的速度、效率则会降低。所以去重统计的场景一般会先使用group by再count来替换。虽然会多用一个Job来完成，但是处理数据量大的场景时，这绝对是值得的。

```sql
-- 项目实测:
-- 设置5个reduce
set mapreduce.job.reduce=5;

select count(distinct id) from bigtable;
-- Stage-Stage-1: Map:1 Reduce:1
-- Time taken:23.607 seconds, Fetched:1 row(s)
-- Time taken:34.941 seconds, Fetched:1 row(s)

select count(id) from (select id from bigtable group by id) t1;
-- Stage-Stage-1: Map:1 Reduce:5
-- Stage-Stage-2: Map:3 Reduce:1
-- Time taken:50.795 seconds, Fetched:1 row(s)
```

### Ⅵ：行列过滤

列处理：在select时，尽可能只拿需要的列，如果有分区字段尽量使用分区过滤，少用select * 。

行处理：表关联时，提前将要关联的表进行过滤，去除掉无用数据，然后再两表关联。

```sql
-- 例:
select t2.* from t1 join t2 on t1.id = t2.id;

select t2.* from t1 join (select * from t2 where id is not null) on t1.id = t2.id;
```

### Ⅶ：动态分区调整

关系型数据库中，对分区表insert数据时，数据库会自动根据分区字段的值，将数据插入到相应的分区中，Hive也提供了类似的机制，即动态分区，只不过使用Hive动态分区时需要进行相应的配置。

```sql
-- 开启动态分区参数(默认开启)
set hive.exec.dynamic.partition=true;

-- 设置为非严格模式(strict | nonstrict)
-- (动态分区的模式，默认严格，表示必须指定至少一个分区为静态分区，非严格表示允许所有分区字段都可以使用动态分区)
set hive.exec.dynamic.partition.mode=nonstrict;

-- 在所有执行MR的节点上，最大一共可以创建多少个动态分区
set hive.exec.max.dynamic.partitions=1000;

-- 在每个执行MR的节点上，最大可以创建多少个动态分区
-- (该参数需要结合实际的数据来设定，比如：源数据包含一年的数据，date分区字段有365个值，该参数则应设置大于365，如果使用默认值100，则会报错)
set hive.exec.max.dynamic.partitions.pernode=100;

-- 整个MR Job中，最大可以创建多少个HDFS文件
set hive.exec.max.created.files=100000;

-- 当有空分区生成时，是否抛出异常。一般不需要设置。
set hive.error.on.empty.partition=false;
```

```sql
-- 例:
set hive.exec.dynamic.partition=true;
set hive.exec.dynamic.partition.mode=nonstrict;
set hive.exec.max.dynamic.partitions=1000;
set hive.exec.max.dynamic.partitions.pernode=100;
set hive.exec.max.created.files=100000;
set hive.error.on.empty.partition=false;

insert overwrite table xxxx partition(date)
select xxxx from xxxx;

-- 查看分区表的分区情况
show partitions table_name;
```

### Ⅷ：分桶

分桶应用场景不多，主要可以用在抽样检测上。比较好理解，只做简单说明。

分桶字段必须为表中已存在字段，在建表时指明分桶字段 clustered by xxxx into N buckets，对每条数据的该分桶字段Hash取模，结果值相同的置于同一桶中。建表时分为几桶，HDFS上即有几个文件，可以打散key以缓解数据倾斜问题。

```sql
create table xxxx
...
clustered by xxxx into N buckets;
```

### Ⅸ：分区

分区表的应用场景就显而易见了，大多都是以时间字段作为分区字段。分区字段是一个全新的不同于表中的字段。分区的作用就是避免全表扫描，查表时可以到指定分区查询，避免了全表扫描所造成的一定的效率问题。

```sql
create table xxxx
...
partition by (dt string);

-- 查看分区表的分区情况
show partitions table_name;
```

## ④ 数据倾斜

### Ⅰ：Map数

Map个数的决定因素主要有：输入文件的总个数、输入文件的大小、集群设置的文件块大小等

通常来讲，Map端输入文件会被切分为多个Block块(默认128M，可参数控制)，有多少个Block块走MR时就对应产生多少个Map Task。

两个结论：

① Map个数不是越多越好

任务小文件很多，切块时会导致Block块数量极多，对应的Map个数也会变多，一个Map任务的启动和初始化时间甚至远大于处理任务的时间，这就会造成资源的浪费，而且同时可执行的Map数量也是受限的。

② 不是保证 每个Map都处理接近128M的文件块就好了

127M的文件理论上是用一个Map去完成，但这个文件只有一两个小字段，却有几千万条数据，如果Map处理的逻辑比较复杂，用一个Map去做就更耗时。

针对这两个结论，我们需要结合业务场景增加或减少Map个数

### Ⅱ：小文件合并

Map执行前合并小文件，以减少Map数：CombineHiveInputFormat具有对小文件进行合并的功能，从而提高任务的执行效率

```sql
set hive.input.format = org.apache.hadoop.hive.ql.io.CombineHiveInputFormat;
```

### Ⅲ：复杂文件增加Map数

当输入文件很大，任务计算逻辑复杂，Map执行就会非常慢，此时可以考虑增加Map个数，从而提高执行效率。

增加Map的方法：

根据ComputeSliteSize(Math.max(minSize, Math.min(maxSize, blocksize))) = blocksize = 128M的公式，调整maxsize的最大值，即减小maxsize设置的值使其小于blocksize的128M，就可以增加Map的个数。

``` sql
-- 例:
select count(1) from xxxx;
-- Hadoop jon information for Stage-1: number of mapper: 1; number of reducers: 1.

-- 调小最大切片值为100M
set mapreduce.input.fileinputformat.split.maxsize=100;
select count(1) from xxxx;
-- Hadoop jon information for Stage-1: number of mapper: 6; number of reducers: 1.
```

### Ⅳ：Reduce数

方法一：

(1)	每个Reduce处理的数据量默认是256M

hive.exec.reducers.bytes.per.reducer=256000000;

(2)	每个任务最大的reduce数，默认为1009

hive.exec.reducers.max=1009

(3)	计算reducer的公式

N = min( 参数2， 文件总输入数据量 / 参数1)		参数1、2对应上面的(1)、(2)

方法二：

在hadoop的mapred-default.xml文件中修改

设置每个Job的Reduce个数：set mapreduce.job.reduces=15;

注：reduce个数也不是越多越好，它的启动与初始化需要时间和资源。有多少个Reduce就会有多少个输出文件，也会导致小文件过多的问题。

**处理大数据量利用合适的Reduce数，使单个Reduce任务处理数据量大小要合适。**

后续待补充从资料第57页开始













