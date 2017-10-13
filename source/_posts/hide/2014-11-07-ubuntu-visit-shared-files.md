---
layout: default
title: ubuntu 下访问 windows 共享文件夹
date: 2014-11-07 11:16:09
tag: 共享文件夹 ubuntu
---

1. 执行如下命令：
```
mount //192.168.3.125/shares –o user=username,pass=pw /mnt/mountpoint
```
需要注意的是 user 与 pass 是共享主机的 user 与 pass，不是访问用户的。
2. 图形模式下：
打开住文件夹，按 ctrl+l, 输入 smb://192.168.3.125/shares
