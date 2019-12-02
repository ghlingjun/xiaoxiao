---
title: 浪潮云服务器规划与配置
comments: true
categories:
  - 技术
tags: []
date: 2019-11-29 14:18:03
updated: 2019-11-29 14:18:03
---

- 创建业务组
- 创建 VPC
- 创建 SLB
- 创建 ECS
- 配置 SLB

## 创建用户组及用户
[参考]https://www.cyberciti.biz/faq/howto-linux-add-user-to-group/

```
# 查看用户组是否存在
grep shumei /etc/group
# 添加用户组 shumei
groupadd shumei

# 查看用户 ethan 是否存在
grep ethan /etc/passwd
# 添加用户，推荐用户名与目录名一致
useradd -g shumei -d /home/shumei -m ethan
# 查看 ethan 信息
id ethan
# 设置用户密码
passwd ethan

# 赋给用户 sudo 权限：http://man.linuxde.net/sudo
vi /etc/sudoers
# 仿照现有root的例子就行，加一行（最好用tab作为空白）
ethan  ALL=(ALL)   ALL
```
Ubuntu 创建的用户为普通账户，默认 shell 为 /bin/sh，需要将账号的 shell 修改为 /bin/bash
```
# echo #SHELL
# usermod -s /bin/bash ethan
```

## 安装 JDK

```
scp -P port jdk-8u25-linux-x64.tar.gz ethan@IP:/path

vi .bashrc
export JAVA_HOME=/home/ethan/software/jdk1.8.0_25
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

## 安装 MySql 服务（5.7）

```
# sudo apt-get update
# sudo apt-get install mysql-server
初始化配置
# sudo mysql_secure_installation
检查服务状态
# systemctl status mysql.service

root 权限登录MySql
# sudo mysql -u root -p
可以修改（也可以不修改） plugin 实现普通用户也能使用 mysql 的 root 用户来登录，同时修改 root 密码
mysql> update user set authentication_string=PASSWORD("123456"), plugin="mysql_native_password" where user="root";
```

### 修改编码配置
查看编码
```
show variables like 'character%';
```
修改 mysqld.cnf，在文件的[mysqld]中增加如下内容：
```
# sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
character-set-server=utf8
```
修改mysql.cnf文件，在[mysql]中增加如下内容：
```
# sudo vi /etc/mysql/conf.d/mysql.cnf
default-character-set=utf8
```

### 大小写敏感设置
修改 mysqld.cnf，在文件的[mysqld]中增加如下内容：
```
# show variables like 'lower%';
# sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
# 表名存储在磁盘是小写的，但是比较的时候是不区分大小写
lower_case_table_names = 1
```

### 修改远程登录权限
```
# sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
# 注释 bind-address
```

### 修改 sql_mode
```
# select @@global.sql_mode
# 重新设置值，添加 sql_mode 配置
# ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
# sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
sql_mode=STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

### 重启服务
```
# sudo /etc/init.d/mysql restart
```

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
```
