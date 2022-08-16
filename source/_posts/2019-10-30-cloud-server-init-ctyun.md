---
title: 电信云服务器规划以及配置
comments: true
categories:
  - 技术
tags: [linux,server]
date: 2019-10-30 22:46:01
updated: 2019-11-29 13:46:01
---

### 挂载磁盘
查看分区
```
df -h
```

查看磁盘
```
fdisk -l
```

格式化分区
```
fdisk /dev/xvde
```

按顺序执行以下命令：
p # 避免错误先查看下现在磁盘的已存在分区，新磁盘没有分区
n # 新建分区——选择p
t # 转换分区格式为8e
w # 保存新建分区格式。

格式化磁盘
```
mkfs -t ext4 /dev/xvde1 
mount /dev/xvde1 /home
```

查看挂载结果
```
df -TH
```

### 修改 root 密码

```
id # 查看用户信息
passwd # 修改用户密码
```

### 创建用户组及用户
[参考]https://www.cyberciti.biz/faq/howto-linux-add-user-to-group/

```
# 查看用户组是否存在
grep shumei /etc/group
# 添加用户组 shumei
groupadd shumei

# 查看用户 shumei 是否存在
grep shumei /etc/passwd
# 添加用户
useradd -g shumei -d /home/shumei -m shumei
# 查看 shumei 信息
id shumei
# 设置用户密码
passwd shumei

# 赋给用户 sudo 权限：http://man.linuxde.net/sudo
vi /etc/sudoers
# 仿照现有root的例子就行，加一行（最好用tab作为空白）
shumei  ALL=(ALL)   ALL
```

Ubuntu 创建的用户为普通账户，默认 shell 为 /bin/sh，需要将账号的 shell 修改为 /bin/bash
```
# echo #SHELL
# usermod -s /bin/bash shumei
```

### 安装 JDK

```
scp jdk-8u25-linux-x64.tar.gz shumei@IP:port/path

vi .bashrc
export JAVA_HOME=/home/shumei/software/jdk1.8.0_25
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

### 安装 MySql 服务

```
# 查找需要安装的文件名
yum search mysql
# 安装文件 5.1
sudo yum install mysql-server.x86_64 -y
# 5.7
https://www.cnblogs.com/lzj0218/p/5724446.html

# 修改数据库配置(myspace/my.cnf)
scp /Users/ethan/docs/scripts/config ethan@ip:/home/shumei
sudo cp my.cnf /etc/my.cnf

sudo /etc/init.d/mysqld start # 启动数据库服务
# 无法启动删除以下文件
sudo rm -rf /var/lib/mysql

sudo mysql # 登陆 MySql
# 创建用户
# https://ghlingjun.github.io/xiaoxiao/2016/09/12/MySql/
create user 'octopus'@'%' identified by 'octopus';

create database owl default character set utf8;
grant all privileges on owl.* to octopus@'%' with grant option;
```

### 安装Nginx

```
http://www.cnblogs.com/rainy-shurun/p/6192753.html

nignx 中 autoindex 要配置成 off

修改 MySQL 默认端口
```
