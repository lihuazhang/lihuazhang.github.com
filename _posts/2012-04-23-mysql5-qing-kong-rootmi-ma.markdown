---
layout: post
title: "mysql5 清空root密码"
date: 2012-04-23 21:27
comments: true
categories: mysql
---
最近在windows用mysql，在root上设置了密码，很不方便，于是就想去清空它，网上找了下教程，总结了下。肯定好用。

* 停止MySQL的服务。 

* 用mysqld.exe --skip-grant-tables & (会占用一个dos控制台窗口)

* 重新打开一个dos控制台窗口，进入mysql 

```bash
mysql -uroot
use mysql
update user set password=password("") where user='root';
flush privileges；
```

* 关闭MySQL的控制台窗口，用正常模式启动Mysql 

* 你可以用新的密码链接到Mysql了。 
