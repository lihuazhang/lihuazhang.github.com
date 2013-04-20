---
layout: post
title: "windows 下查找占用端口的进程"
date: 2013-03-21 21:02
comments: true
categories: windows
---

一直只知道 *nux 下的命令，对 windows 知之甚少，其实 windows 下也有很多强大的命令。

比如在 linux 下查看一个 mysql 的端口，

```

➜  octopress git:(source) ✗ netstat -an | grep 3306 # mac 下的命令没有 linux 下来的丰富啊，没有 p 选项。
tcp4       0      0  *.3306                 *.*                    LISTEN

root@li493-58:~# netstat -anp | grep 3306 # 这是 ubuntu 下面的。
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      2509/mysqld

```

很方便吧。那么如果在 windows 下呢？

```

netstat -ano | findstr 3306 # 这里会把 pid 告诉你，比如 pid 是 1278

tasklist /fi "pid eq 1278" # 这样就找到了 1278 对应的进程
```

也是很简单的吧！
