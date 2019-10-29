---
layout: default
title: 连接 Mysql 遇到错误代码 10061
date: 2014-11-07 10:20:09
tag: mysql
---

安装 Mysql 后，远程连接报 10061 错误，修复办法如下：
```
sudo vi /etc/mysql/my.conf
```
注释掉 bind-address = 127.0.0.1
 
重启 Mysql 服务：
```
sudo /etc/init.d/mysql restart
```
