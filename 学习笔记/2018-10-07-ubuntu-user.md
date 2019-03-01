---
title: Ubuntu创建新用户的正确姿势
date: 2018-10-07
categories:
tags:
- 服务器
- Ubuntu
permalink: "2018-10-07-ubuntu-user"
---

最近在学习《系统编程》，老师让创建新的用户，以`name+学号`的格式命名，来防止抄袭。

因此，每次到一台新电脑，都要在 ubuntu 上创建新用户。**然而，`sudo useradd 用户名`只能创建用户，却无法在`/home/`中创建用户目录，也无法设置用户权限。**

查了很多篇博客，为了方便查阅，记录一下创建新用户并且分配权限的正确方法。

<!-- more -->

## 1. 前言

最近在学习《系统编程》，老师让创建新的用户，以`name+学号`的格式命名，来防止抄袭。

因此，每次到一台新电脑，都要在 ubuntu 上创建新用户。然而，`sudo useradd 用户名`只能创建用户，却无法在`/home/`中创建用户目录，也无法设置用户权限。

查了很多篇博客，为了方便查阅，记录一下创建新用户并且分配权限的正确方法。

## 2. 创建用户目录

创建新用户：`sudo useradd -r -m -s /bin/bash dongyuanxin_2016150127`。

**在 Ubuntu18.04 中，不会在创建用户的时候自动提示设置密码。需要手动执行：`sudo passwd dongyuanxin_2016150127`。来设置新用户的密码。**

其中参数的意义如下：

```
-r：建立系统账号
-m：自动建立用户的登入目录
-s：指定用户登入后所使用的shell
```

输入`ls /home/`，可以看到用户目录被成功创建了：

![](/images/Linux/Ubuntu创建新用户的正确姿势/1.png)

## 3. 修改用户权限

这里采用修改`/etc/sudoers`文件的方法分配用户权限。因为此文件只有`r`权限，在改动前需要增加`w`权限，改动后，再去掉`w`权限。

```shell
sudo chmod +w /etc/sudoers
sudo vim /etc/sudoers
# 添加下图的配置语句，并且保存修改
sudo chmod -w /etc/sudoers
```

![](/images/Linux/Ubuntu创建新用户的正确姿势/2.png)

到此，新用户创建成功，并且用户目录被创建，权限也分配成功。如下图所示：

![](/images/Linux/Ubuntu创建新用户的正确姿势/3.png)

## 4. 删除用户

删除用户的操作分为 3 步：

1. 执行`userdel`：`sudo userdel dongyuanxin_2016150127`
2. 删除用户目录：`sudo rm -rf /home/dongyuanxin_2016150127`
3. 删除用户权限相关配置：删除或者注释掉`/etc/sudoers`中关于要删除用户的配置，否则无法再次创建同名用户。

## 5. 资料参考

- [USERADD 命令详解](https://blog.csdn.net/davil_dev/article/details/6942128)
- [Linux 中 sudo 的用法和 sudoers 配置详解](https://www.linuxidc.com/Linux/2016-08/134451.htm)
