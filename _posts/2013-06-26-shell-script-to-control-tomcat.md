---
layout: post
title: "能马上用起来的控制 tomcat 的脚本"
description: ""
categories: linux
tags: []
---

难的在网上找到的能马上用起来的 shell 脚本，心情愉悦啊！


``` bash
#!/bin/bash

export BASE=/home/programs/jakarta-tomcat-5.0.28/bin
prog=jakarta-tomcat-5.0.28

stat() {
if [ `ps auxwwww|grep $prog|grep -v grep|wc -l` -gt 0 ]
then
echo Tomcat is running.
else
echo Tomcat is not running.
fi
}

case "$1" in
start)

if [ `ps auxwwww|grep $prog|grep -v grep|wc -l` -gt 0 ]
then
echo Tomcat seems to be running. Use the restart option.
else
$BASE/startup.sh 2>&1 > /dev/null
fi
stat
;;
stop)
$BASE/shutdown.sh 2>&1 > /dev/null
if [ `ps auxwwww|grep $prog|grep -v grep|wc -l` -gt 0 ]
then
for pid in `ps auxwww|grep $prog|grep -v grep|tr -s ' '|cut -d ' ' -f2`
do
kill -9 $pid 2>&1 > /dev/null
done
fi
stat
;;
restart)

if [ `ps auxwwww|grep $prog|grep -v grep|wc -l` -gt 0 ]
then
for pid in `ps auxwww|grep $prog|grep -v grep|tr -s ' '|cut -d ' ' -f2`
do
kill -9 $pid 2>&1 > /dev/null
done
fi
$BASE/startup.sh 2>&1 > /dev/null
stat
;;
status)
stat
;;
*)
echo "Usage: tomcat start|stop|restart|status"
esac
```

参见 http://www.techpaste.com/2012/01/shell-script-control-stopstartrestartstatus-tomcat-application-server/
