---
title: Mysql无法选取非聚合列
date: 2018-08-11
categories:
tags:
- Mysql
- 数据库
permalink: "2018-08-11-mysql-unagreegate-col"
---

`Mysql 5.7`默认是无法选取非聚合列。如果要选取一条记录前后两条相邻记录，就会抛出 _SELECT list is not in GROUP BY clause ..._ 的报错。

需要手动修改配置文件，显示声明`sql_mode`选项。

<!-- more -->

### 1. 前言

最近升级博客，给文章页面底部增加了两个按钮，可以直接跳转到上一篇和下一篇。如下图所示：
![效果图](/images/Mysql/Mysql无法选取非聚合列/1.png)

> 实现这个功能的难点在于：数据库怎么选取出一条记录的前后两条相邻的记录？

### 2. 数据库设计

关于我文章数据库的设计如下图所示：
![数据库设计](/images/Mysql/Mysql无法选取非聚合列/2.png)

可以看到，每条记录的身份是索引`Id`。因为之前有很多文章记录被删除了，所以，`Id`并不是连续的。

如果当前文章的索引值是`33`，那么可以通过以下命令来得到前后相邻的 2 篇文章：

```sql
select * from passage where id in
(select
case
when SIGN(id - 32 )>0 THEN MIN(id)
when SIGN(id - 32 )<0 THEN MAX(id)
end
from passage
where id != 34
GROUP BY SIGN(id- 32 )
ORDER BY SIGN(id- 32 )
)
ORDER BY id;
```

### 3. 无法选取聚合列

在执行上面命令时，`Mysql`给了我个: _SELECT list is not in GROUP BY clause ..._ 的报错。经过 Google 得知，`mysql 5.7`以上，默认启动了`only_full_group_by`，MySQL 就会拒绝选择列表、条件或顺序列表引用的查询。

以下是原文：

> Reject queries for which the select list, HAVING condition, or ORDER BY list refer to nonaggregated columns that are neither named in the GROUP BY clause nor are functionally dependent on (uniquely determined by) GROUP BY columns.
> As of MySQL 5.7.5, the default SQL mode includes ONLY_FULL_GROUP_BY. (Before 5.7.5, MySQL does not detect functional dependency and ONLY_FULL_GROUP_BY is not enabled by default. For a description of pre-5.7.5 behavior, see the MySQL 5.6 Reference Manual.)

所以，我们应该设置`sql_mode`中**不包含`only_full_group_by`选项**。

进入 mysql 配置文件，在`[mysqld]`部分中添加以下配置，并且重启 mysql 即可。

```shell
[mysqld]
# ... other config
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATEERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION # delete 'only_full_group_by'
# ... other config
```

运行本文第二部分的 mysql 的命令，结果如下图所示：
![运行结果](/images/Mysql/Mysql无法选取非聚合列/3.png)

### 4. 相关链接

- [only_full_group_by](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sqlmode_only_full_group_by)
