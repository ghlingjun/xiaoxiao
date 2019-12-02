---
title: linux 常用命令
date: 2019-10-29 17:55:39
updated: 2019-10-30 17:55:39
comments: true
categories: []
tags: [linux,du,mount,ssh,curl]
---

## 命令介绍
### du
当前目录只进入第一级目录
du -h --max-depth=1
du -h -d1

### free 查看内存情况
free -m

### top
top

### vmstat
vmstat

### 挂在硬盘
blkid -o device
mount /dev/xvde1 /home

### 重启定时任务
sudo /etc/init.d/crond restart

### 修改密码
id # 查看用户信息
passwd # 修改用户密码

### 修改 ssh 服务端口号
查看端口是否被占用：
sudo netstat -anp | grep 10007
添加配置：
sudo vi /etc/ssh/sshd_config
Port 10007
重启 ssh 服务：
sudo service sshd restart

### curl
curl -Iv www.baidu.com
curl -Iv http://172.17.0.15
curl 129.211.135.212:80

### telnet
telnet 172.17.0.15 80

### 查看系统版本
lsb_release -a

##其他
```
80,8080,443,8443 需要备案
```
### 环境变量配置
alias sshdb="ssh ethan@192.168.1.79"

export JAVA_HOME=/home/shumei/software/jdk1.8.0_25
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
