---
title: Mysql支持emoji表情
date: 2018-08-09
categories:
- Mysql
tags:
- 字符编码
- 数据库
---

最近为博客添加了`Valine`评论系统，在爬下来评论数据保存到服务器数据库的过程中发现，`mysql`默认配置是不支持`utf8mb4`编码的，无法写入`emoji`表情。

所以，需要手动进行配置，来支持各式各样的表情。

😀😃😄😁😆😅😂😊😇😉😌😍😘😗😙😚😋😜😝😛😎😏😒😞😔😟😕😣😖😫😩😠😡😶😐😑😯😦😧😮😲😵😳😱😨😰😢😥😭😓😪😴😷😈😺😸😹😻😼😽🙀😿😾🐱🐭🐮🐵✋✊✌️👆👇👈👉👊👋👏👐👍👎👌🙏👂👀👃👄👅❤️💘💖⭐️✨⚡️☀️☁️❄️☔️☕️✈️⚓️⌚️☎️⌛️✉️✂️✒️✏️❌♻️✅❎Ⓜ️


<!-- more -->

### 1. 前言

> 最近为博客添加了`Valine`评论系统，因为它用的`Leancloud`的数据库，所以打算写个程序定时爬下来新的数据，并且存到自己的数据库中（_毕竟在自己手中才是最安全的_）。因为评论里面有`emoji`表情，所以需要**数据库支持`utf8mb4`编码**。

### 2. 踩坑

服务器安装的数据库是`Mysql 5.7`。网上很多方法是通过命令行设置字符集编码格式，但是经过尝试，都以失败告终。

摸索后发现，需要更改`mysql`的配置文件。

首先，备份原来的配置文件：`sudo cp /etc/mysql/mysql.conf.d/mysqld.cnf /etc/mysql/mysql.conf.d/mysqld.cnf.bak`

然后，在`mysqld.cnf`文件中添加如下配置：

```shell
[client]
default-character-set = utf8mb4 # 客户端数据默认字符集

[mysql]
default-character-set = utf8mb4 # 数据库默认字符集

[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4 # 服务端默认字符集
collation-server = utf8mb4_unicode_ci # 连接层默认字符集
init_connect = 'SET NAMES utf8mb4' # 指定每次连接的字符集utf8mb4
```

最后，保存退出后，重启`mysql`服务：`sudo service mysql restart`

### 3. 使用

为了方便使用，我一直使用的是`mysql-font`。在创建表格时候，设置字符集是`utf8mb4`，如下图所示：

![设置utf8mb4](/images/Mysql/Mysql支持emoji表情/1.png)

最后，各式各样的`emoji`表情就可以存储在数据库了：

😀😃😄😁😆😅😂😊😇😉😌😍😘😗😙😚😋😜😝😛😎😏😒😞😔😟😕😣😖😫😩😠😡😶😐😑😯😦😧😮😲😵😳😱😨😰😢😥😭😓😪😴😷😈😺😸😹😻😼😽🙀😿😾🐱🐭🐮🐵✋✊✌️👆👇👈👉👊👋👏👐👍👎👌🙏👂👀👃👄👅❤️💘💖⭐️✨⚡️☀️☁️❄️☔️☕️✈️⚓️⌚️☎️⌛️✉️✂️✒️✏️❌♻️✅❎Ⓜ️
