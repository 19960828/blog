---
title: Mysql连接缓慢
date: 2018-08-28
categories:
tags:
- Mysql
- 数据库
- 服务器
permalink: "2018-08-28-mysql-low-speed"
---

在客户端和连接逐渐增多的情况下，禁用Mysql的`DNS`解析服务，可以极大提高连接速度，解决`Handshake inactivity timeout`等`TIMEOUT`类型报错。

<!-- more -->

> 最近在 Node 上进行 Mysql 操作的时候，经常会报出：`Handshake inactivity timeout` 错误。而且，使用 Mysql-Font 等工具的链接速度也非常缓慢。

项目为了实现高并发，所以使用的是连接池。在查询了[相关文档](https://www.npmjs.com/package/mysql#pool-options)后，修改了`acquireTimeout`等选项。报错不变。

经过摸索，连接缓慢应该是：**Mysql 自带的 DNS 解析过慢** 造成的。在配置文件中禁用 DNS 解析即可。

我的 Mysql 版本是`5.7`，代开配置文件：`sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf`

添加如下代码：

```shell
# 其他配置...
[mysqld]
skip-name-resolve
# 其他配置...
```

重启 Mysql 服务：`sudo service mysql restart`。

进入 Mysql，查看相关配置：

![](/images/Mysql/Mysql连接缓慢/1.png)

DNS 解析被禁止，而连接速度也恢复了。
