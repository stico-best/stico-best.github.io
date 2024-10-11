---
title: MySQL学习
tags: 2
abbrlink: c6fb831a
date: 2022-06-15 16:07:19
---

**基本的SQL语言学习（MySQL）**

<!--more-->

# SQL的基本定义及概念

**数据库（DataBase）**：存储数据的仓库，数据有组织的进行存储。

**数据库管理系统（DBMS）**：操纵和管理数据库的大型软件。

**SQL**：操作关系型数据库的编程语言。

**关系型数据库（RDBMS）**：建立在关系模型基础上，由多张相互连接的二维表组成的数据库

基础使用方法：通过cmd输入mysql -r root -p进入mysql页面进行操作 或 运行MySQL command line Client - Unicode

**SQL分类**：

| **DDL** | **数据定义语言** | **定义数据库对象（数据库、表、字段）**   |
| ------- | ---------------- | ---------------------------------------- |
| **DML** | **数据操作语言** | **对数据库表中数据增删改**               |
| **DQL** | **数据查询语言** | **查询数据库表中的记录**                 |
| **DCL** | **数据控制语言** | **创建数据库用户，控制数据库的访问权限** |

------

# DDL语句

语句后面注意 “ ; ”	，	[...]部分表示选写内容	，	comment '...' 为注释内容

## 数据库操作

- 查询所有数据库：**show database;**
- 查询当前数据库：**select database();**
- 创建数据库：**create database [if not exists] 数据库名;**
- 删除数据库：**drop database [if not exists] 数据库名;**
- 使用数据库：**use 数据库名;**

## 表操作

- 修改表名：**alter table 表名 rename to 新表名;**
- 删除表：**drop table [if exists] 表名**
- 删除指定表，并重新创建该表（初始化该表）：**truncate table 表名;**

### 查询

- 查询当前数据库所有表：**show tables;**
- 查询表结构：**desc 表名;**
- 查询指定表的建表语句：**show create table 表名;**

### 修改

- 添加字段：**alter table 表名 add 字段名 数据类型;**
- 修改数据类型：**alter table 表名 modify 字段名 新的数据类型;**
- 修改字段名和字段类型：**alter table 表名 change 旧字段名 新字段名 数据类型;** 

### 删除

- 删除字段：**alter table 表名 drop 字段名;**

------

# DML语句

## 添加数据

- 给指定字段添加数据：**insert into 表名(字段名1,字段名2,...) values (值1,值2,...);**
- 给全部字段添加数据：**insert into 表名 values (值1,值2,...);**
- 批量添加数据：**insert into 表名(字段名1,字段名2,...) values (值1,值2,...);**  /*一行*/
- ​						 **insert into 表名 values (值1,值2,...),(值1,值2,...),(值1,值2,...);**  /*多行*/

插入数据时，指定的字段顺序需要与值的顺序是一一对应的。

字符串和日期型数据应该包含在引号中。

插入的数据大小，应该在字段的规定范围内（满足对应的数据类型和数据范围）。

## 修改数据

- **update 表名 set 字段名1=值1,字段名2=值2,... [where 条件];**

修改语句的条件可有可无，若没有where条件，则会修改整张表的所有数据。

## 删除数据

- **delete from 表名 [where 条件];**

删除语句的条件可有可无，若没有where条件，则会删除整张表的所有数据。

delete语句不能删除某个字段的值（可以用update）。

------

# DQL语句

## 基本查询

- 查询多个字段：**select 字段1,字段2,... from 表名;**
- 查询全部字段：**select * from 表名;**
- 设置别名：**select 字段1[as 别名1],字段2[as 别名2],... from 表名;**
- 去除重复记录：**select distinct 字段列表 from 表名;**

## 条件查询

- **select 字段列表 from 表名 where 条件列表;**

例：查询姓名为两个字的员工信息select * from employee where name like '__';

​		查询身份证号最后一位是X的员工信息select * from employee where idcard like '%X';

​		或 select * from employee where idcard like '_________________X' (17个'_')

| 比较运算符       | 功能                                       |
| ---------------- | :----------------------------------------- |
| >                | 大于                                       |
| >=               | 大于等于                                   |
| <                | 小于                                       |
| <=               | 小于等于                                   |
| =                | 等于                                       |
| <> 或 !=         | 不等于                                     |
| between...and... | 介于范围之内（between 小 and 大）          |
| in(...)          | 在in之内的列表中的值，满足一个即可         |
| like 占位符      | 模糊匹配（_匹配单个字符，%匹配任意个字符） |
| is null          | 判断是否为空                               |



| 逻辑运算符 | 功能                         |
| ---------- | ---------------------------- |
| and 或 &&  | 并且（多个条件同时成立）     |
| or 或 \|\| | 或者（多个条件任意一个成立） |
| not 或 ！  | 非，不是                     |

## 聚合函数

- **select 聚合函数(字段列表) from 表名;**

定义：将一列数据作为一个整体，进行纵向计算

null值不参与所有聚合函数的运算

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

## 分组查询

- **select 字段列表 from 表名 [where 条件] group by 分组字段名 [having 分组后过滤条件];**

## 排序查询

- **select 字段列表 from 表名 order by 字段1,排序方式1,字段2,排序方式2,...;**

排序方式有：ASC升序(默认)、DESC降序

## 分页查询

- **select 字段列表 from 表名 limit 起始索引,查询记录数;**

起始索引从0开始，起始索引=(查询页码 - 1) * 每页显示记录数

如果查询的是第一页数据，起始索引可以省略

## DQL语句执行顺序

1. from 表名列表
2. where 条件列表
3. group by 分组字段列表
4. having 分组后条件列表
5. select 字段列表
6. order by 排序字段列表
7. limit 分页参数

------

# DCL语句

数据控制语言，用来管理数据库用户、控制数据库的访问权限。

| 权限（常用）        | 说明               |
| ------------------- | ------------------ |
| all(all privileges) | 所有权限           |
| select              | 查询数据           |
| insert              | 插入数据           |
| update              | 修改数据           |
| delete              | 删除数据           |
| alter               | 修改表             |
| drop                | 删除数据库/表/视图 |
| create              | 创建数据库/表      |



## 管理用户

- 查询用户：use mysql;  =>  **select * from user;**
- 创建用户：**create user 'user name'@'host name' identified by 'password';**
- 修改用户密码：**alter user 'user name'@'host name' identified with mysql_native_password by 'new password';**
- 删除用户：**drop user 'user name'@'host name';**

## 权限控制

- 查询权限：**show grants for 'user name'@'host name';**
- 授予权限：**grant 权限列表 on 数据库名.表名 to 'user name'@'host name';**
- 撤销权限：**revoke 权限列表 on 数据库名.表名 from 'user name'@'host name';**

------

# 函数

## 字符串函数

| 函数（常用）                | 功能                                                      |
| --------------------------- | --------------------------------------------------------- |
| concat(S1, S2, S3, ... ,Sn) | 字符串拼接，将S1...Sn拼接成一个字符串                     |
| lower(str)                  | 将字符串str全部转为小写                                   |
| upper(str)                  | 将字符串str全部转为大写                                   |
| lpad(str, n, pad)           | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度 |
| rpad(str, n, pad)           | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度 |
| trim(str)                   | 去掉字符串头部和尾部的空格                                |
| substring(str, start, len)  | 返回从字符串str从start位置起的len个长度的字符串           |

例：study数据库的employee表中将员工编号workno更改，将编号前补齐'0'以达到五位数编号

答：update employee set wordno = lpad(workno, 5, '0');

------

## 数值函数

| 函数（常用） | 功能                               |
| ------------ | ---------------------------------- |
| ceil(x)      | 向上取整                           |
| floor(x)     | 向下取整                           |
| mod(x, y)    | 返回x/y的模（整除后的余数）        |
| rand()       | 返回0~1内的随机数                  |
| round(x, y)  | 求参数x的四舍五入的值，保留y位小数 |

例：使用数据库的函数，实现随机生成6位验证码

答：lpad(round(select rand() * 1000000, 0), 6, '0');

此方法运用了round()函数，替换成向上或向下取整函数依然可以完成，lpad或rpad同理。

------

## 日期函数

| 函数                               | 功能                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| curdate()                          | 返回当前日期                                                 |
| curtime()                          | 返回当前时间                                                 |
| now()                              | 返回当前日期和时间                                           |
| year(date)                         | 获取指定date的年份                                           |
| month(date)                        | 获取指定date的月份                                           |
| day(date)                          | 获取指定date的日期                                           |
| date_add(date, interval expr type) | 计算date加expr type后对应的时间,expr表示数值，type表示类型如年月日 |
| datediff(date1, date2)             | 计算date1 和 date2之间相隔的天数                             |

函数举例：date_add(now(), interval 90 day) ==>> 2022-07-08 18:54:37

函数举例：datediff('2022-4-9', '2022-7-1') ==>> -83

例：根据study数据库的employee表，计算所有员工入职时间，用天数表示，并按天数降序排序

答：select name, datediff(curdate(), entrydate) from employee order by datediff(curdate(), entrydate) desc;

------

## 流程控制函数

| 函数                                                       | 功能                                                     |
| ---------------------------------------------------------- | -------------------------------------------------------- |
| if(value, t, f)                                            | 如果value为true，则返回t，否则返回f                      |
| ifnull(value1, value2)                                     | 如果value1不为空，返回value1，否则返回value2             |
| case when [val1] then [res1] ... else [default] end        | 如果val1为true，返回res1，...否则返回default默认值       |
| case [expr] when [val1] then [res1] ... else [default] end | 如果expr的值等于val1，返回res1，...否则返回default默认值 |

例：根据study数据库的score表，将学生成绩置换成'优秀'>=85,60=<'及格'=<85,'不及格'<60

答：

```mysql
select
    id,
    name,
    (case when math >= 85 then '优秀' when math < 60 then '不及格' else '及格' end),
    (case when english >= 85 then '优秀' when english < 60 then '不及格' else '及格' end),
    (case when chinese >= 85 then '优秀' when chinese < 60 then '不及格' else '及格' end)
from score;
```

------

# 约束

## 基本定义

概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

目的：保证数据库中数据的正确、有效性和完整性。

分类：

| 约束     | 描述                                                     | 关键字      |
| -------- | -------------------------------------------------------- | ----------- |
| 非空约束 | 限制该字段的数据不能为null                               | not null    |
| 唯一约束 | 保证该字段的所有数据都是唯一、不重复的                   | unique      |
| 主键约束 | 主键是一行数据的唯一标识，要求非空且唯一                 | primary key |
| 默认约束 | 保存数据时，如果未指定该字段的值，则采用默认值           | default     |
| 检查约束 | 保证字段值满足某一个条件                                 | check       |
| 外键约束 | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | foreign key |

注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。

例：

{% asset_img 1.png  举例完成需求 %}

```mysql
create table user
(
 id int primary key auto_increment comment '主键',
 name varchar(10) not null unique comment '姓名',
 age int check (age>0 && age<=120) comment '年龄',
 status char(1) default '1' comment '状态',
 gender char(1) comment '性别'
)comment '用户表';
```

------

## 外键约束

### 基本定义

概念：外键用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

注意：在数据库层面，未建立外键关联的两张表之间通过外键约束，是无法保证数据的一致性和完整性的。

------

### 语法

添加外键：

- **create table 表名(**
- ​		**字段名		数据类型,**
- ​		**......			 ......**
- ​		**[constraint] [外键名称] foreign key (外键字段名) references 主表(主表列名)**
- **);**

或：

- **alter table 表名 add constraint 外键名称 foreign key(外键字段名) references 主表(主表字段名);**

删除外键：

- **alter table 表名 drop foreign key 外键名称;**

------

### 删除/更新行为

- **alter table 表名 add constraint 外键名称 foreign key(外键字段名) references 主表(主表字段名) on update cascade on delete cascade;**

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| no action   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新(与restrict一致) |
| restrict    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新(与no action一致) |
| cascade     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录 |
| set null    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null(这就要求该外键允许取null) |
| set default | 父表有变更时，子表将外键列设置成一个默认的值(innodb不支持)   |

------

# 多表查询

## 多表关系

项目开发中，在进行数据库表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：一对多、多对多、一对一。

### 多对多关系

实现：建立第三张中间表，中间至少包含两个外键，分别关联两方主键

常见多对多关系如：学生与课程

{% asset_img student_course.png 学生与课程多对多关系表%}

### 一对一关系

关系：一对一关系，多用于单标拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中。

实现：在任意一方加入外键，关联另外一方的主键，并且**设置外键为唯一的(UNIQUE)**

## 多表查询分类

定义：指从多张表中查询数据

**连接查询：**

内连接：相当于查询A、B交集部分数据

外连接：	

- 左外连接：查询左表所有数据，以及两张表交集部分数据
- 右外连接：查询右表所有数据，以及两张表交集部分数据

自连接：当前表与自身的连接查询，自连接必须使用表别名

子查询

## 多表查询语句

### 内连接

隐式内连接：**select 字段列表 from 表1, 表2 where 条件...;**

显式内连接：**select 字段列表 from 表1 [inner] join 表2 on 连接条件...;**



### 外连接

查询表1的所有数据 包含表1和表2交集部分的数据)：

**select 字段列表 from 表1 left [outer] join 表2 on 条件...;**

右外连接(相当于查询表2的所有数据 包含表1和表2交集部分的数据)：

**select 字段列表 from 表1 right [outer] join 表2 on 条件...;**



### 自连接

**select 字段列表 from 表A 别名A join 表A 别名B on 条件...;**



### 子查询

SQL语句中嵌套select语句，称为嵌套查询，又称子查询

**select * from t1 where column1= (select column1 from t2);**

子查询外部的语句可以是insert/update/delete/select的任何一个

根据子查询结果不同，分为：

- 标量子查询(子查询结果为单个值，常用的操作符：==、<>、>、>=、<、<=)
- 列子查询(子查询结果为一列，常用的操作符：in、not in、any、some、all)
- 行子查询(子查询结果为一行，常用的操作符：=、<>、in、not in)
- 表子查询(子查询结果为多行多列，常用的操作符：in)

根据子查询位置，分为：where之后、from之后、select之后

------

# 联合查询-union

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致

union all会将全部的数据直接合并在一起输出，union会对合并之后的数据去重再输出

**select 字段列表 from 表A ...**

**union [all]**

**select 字段列表 from 表B ...;**

------

# 事务

事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

## 事务操作

查看/设置事务提交方式：

**select @@autocommit;**

**set @@autocommit = 0;**  ## 切换为手动提交

提交事务：**commit;**

回滚事务：**rollback;**

## 事务四大特性ACID

- 原子性：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
- 一致性：事务完成时，必须使所有的数据都保持一致状态。
- 隔离性：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。
- 持久性：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

## 并发事务问题

| 问题       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 脏读       | 一个事务读到另外一个事务还没有提交的数据                     |
| 不可重复读 | 一个事务先后读取同一条记录，但两次读取的数据不同             |
| 幻读       | 一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时又发现这行数据 |

## 事务隔离级别

|     隔离级别     | 脏读 | 不可重复读 | 幻读 |
| :--------------: | :--: | :--------: | :--: |
| read uncommitted |  √   |     √      |  ×   |
|  read committed  |  ×   |     √      |  √   |
| repeatable read  |  ×   |     ×      |  √   |
|   serializable   |  ×   |     ×      |  ×   |

