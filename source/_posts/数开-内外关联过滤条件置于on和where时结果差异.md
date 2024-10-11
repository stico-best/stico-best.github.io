---
title: 数开-内外关联过滤条件置于on和where时结果差异
abbrlink: 2f22026c
date: 2023-05-26 18:17:24
tags:
---

**Join表关联on过滤与where过滤造成指标计算结果差异**

<!--more-->

项目中，结合精算师给的模板计算精算指标 prem 时尝试在Join on条件写入筛选条件。

从测试环境数仓，ODS层客户投保记录表拿了500条数据，需要关联APP层客户投保详情记录表数据，（APP层粒度更高，记录除客户信息外的保险ID 投保时间等信息）。

计算指标：first_prem 每个客户首年保费、total_prem 客户总交保费、first_of_total_prem 保费收入比

测试数据 指标计算：

```sql
select
    prem_std,
    least(
        cast(ppp as int),
        policy_year,
        floor(months_between(elapse_date, t1.buy_datetime) / 12) + 1
    ) as ppp_year
from insurance_app.policy_result t1
left join insurance_ods.policy_surrender t2 on t1.pol_no = t2.pol_no
where t1.month = '2021-04';
```

```sql
select
    prem_std,
    least(
        cast(ppp as int),
        policy_year,
        floor(months_between(elapse_date, t1.buy_datetime) / 12) + 1
    ) as ppp_year
from insurance_app.policy_result t1
left join insurance_ods.policy_surrender t2 on t1.pol_no = t2.pol_no
and t1.month = '2021-04'
```

{% asset_img 1.png 测试数据差异结果 %}

正确结果应该是第一行数据，这个问题就对应上 SQL的外关联(left join | right join | full join)的特点：

无论哪种外关联，都会将两表的并集，和主表的全部 取出，而 Join的 on 条件仅仅是对 Join 的匹配条件进行筛选过滤，where则是在两表关联完成以后，对关联后的所谓临时表数据进行过滤。

**即，on 过滤条件 针对 连接时的匹配，关联后不会减少主表的行数，主表的所有记录均仍保留**

**where 过滤条件 会作用于整个结果集，可以理解为 关联后的临时表 再过滤，故会再根据where条件筛选掉部分数据。**

------

项目保费指标 prem 是 数值结果计算，从数值上不够明显，下面举个简单的例子就好。

{% asset_img 2.png 举个栗子 %}

不加过滤条件 单独left join时:

```sql
SELECT e.emp_name, p.project_name
FROM employees e
LEFT JOIN projects p
ON e.emp_id = p.emp_id
```

| emp_name | project_name |
| -------- | ------------ |
| Alice    | Project A    |
| Bob      | Project B    |
| Bob      | Project C    |
| Charlie  | NULL         |

on 上 加过滤条件时：

```sql
SELECT e.emp_name, p.project_name
FROM employees e
LEFT JOIN projects p
ON e.emp_id = p.emp_id AND p.project_name = 'Project A';
```

| emp_name | project_name |
| -------- | ------------ |
| Alice    | Project A    |
| Bob      | NULL         |
| Charlie  | NULL         |

where 上 加过滤条件时：

```sql
SELECT e.emp_name, p.project_name
FROM employees e
LEFT JOIN projects p
ON e.emp_id = p.emp_id
WHERE p.project_name = 'Project A';
```

| emp_name | project_name |
| -------- | ------------ |
| Alice    | Project A    |







