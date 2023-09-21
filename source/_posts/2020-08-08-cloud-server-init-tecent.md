---
title: 腾讯云服务器部署
comments: true
categories:
  - tech
tags: [server]
date: 2020-08-08 10:58:28
updated: 2020-08-08 10:58:28
---

执行以下命令，查看连接到实例的磁盘名称
sudo fdisk -l
执行以下命令，格式化该磁盘
sudo mkfs.ext4 /dev/vdb
执行以下命令，将该磁盘挂载到 /data 挂载点
sudo mount /dev/vdb /data
创建用户组及用户
[参考]https://www.cyberciti.biz/faq/howto-linux-add-user-to-group/

# 用户创建
查看用户组是否存在
grep shumei /etc/group
添加用户组 shumei
groupadd shumei

查看用户 shumei 是否存在
grep shumei /etc/passwd
添加用户
useradd -g shumei -d /data/home/shumei -m shumei
查看 shumei 信息
id shumei
设置用户密码
passwd shumei

赋给用户 sudo 权限：http://man.linuxde.net/sudo
vi /etc/sudoers
仿照现有root的例子就行，加一行（最好用tab作为空白）
shumei  ALL=(ALL)   ALL
Ubuntu 创建的用户为普通账户，默认 shell 为 /bin/sh，需要将账号的 shell 修改为 /bin/bash
```
echo $SHELL
usermod -s /bin/bash shumei
```

```
export JAVA_HOME=/data/home/shumei/software/jdk1.8.0_221
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

```
create user 'shumei'@'%' identified by '密码';

create database fund_management default character set utf8;
grant all privileges on fund_management.* to shumei@'%' with grant option;
```

```
server {
    listen       80;
    server_name  gx.digital-fund.cn;

    access_log off;
    rewrite ^(.*)$ https://$host$1 permanent;

}
```
