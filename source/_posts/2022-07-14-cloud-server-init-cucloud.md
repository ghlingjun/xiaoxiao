---
title: 联通云服务器初始化
comments: true
categories:
  - 运维
tags: [server, 联通云]
date: 2022-07-14 18:43:55
updated: 2022-07-14 18:43:55
---

# 挂载并格式化磁盘
查看磁盘
fdisk -l

格式化磁盘
sudo mkfs.ext4 /dev/vdb
mount /dev/vdb /home

查看挂载结果
df -TH

# 修改 root 密码
id # 查看用户信息
passwd # 修改用户密码

# 修改 ssh 默认端口
sudo vi /etc/ssh/sshd_config
修改 22 为 10021
Port 10021
重启 ssh 服务
sudo systemctl restart ssh

# 创建用户组及用户
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
shumei  ALL=(ALL:ALL)   ALL
```

Ubuntu 创建的用户为普通账户，默认 shell 为 /bin/sh，需要将账号的 shell 修改为 /bin/bash
```
# echo $SHELL 可查看当前使用的 shell
usermod -s /bin/bash shumei
```

# 安装 JDK
```
scp jdk-8u25-linux-x64.tar.gz shumei@IP:port/path

vi .profile
export JAVA_HOME=/home/shumei/software/jdk1.8.0_25
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

# 使配置生效
source .profile

# 检查配置是否成功
java -version
```

# 安装 Nginx
[以下仅示例，具体请参考官网文档]https://nginx.org/en/linux_packages.html#Ubuntu
```
wget http://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key

sudo vi /etc/apt/sources.list.d/nginx.list
# 添加如下内容
deb http://nginx.org/packages/mainline/ubuntu/ xenial nginx
deb-src http://nginx.org/packages/mainline/ubuntu/ xenial nginx

sudo apt-get update
sudo apt-get install nginx
```

# Redis安装
```
# 安装
sudo apt install redis-server

查看是否启动
# redis-cli
以上命令将打开以下终端：
redis 127.0.0.1:6379>
```
