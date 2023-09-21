---
title: Ubuntu Server 安装
comments: true
categories:
  - tech
tags: [ubuntu]
date: 2021-03-29 16:54:42
updated: 2021-03-29 16:54:42
---

# 下载系统 ISO 文件
https://cn.ubuntu.com/download
Ubuntu Server 20.04.2 LTS

# 安装
1. 将 ISO 文件解压至 USB Disk 根目录
2. 启动电脑时，进入手动选择模式（通常是 F12、F10、F2）
3. 安装系统

# 配置静态IP
查看IP
```
ip addr
```

修改配置文件
```
vi /etc/netplan/00-installer-config.yaml

# This is the network config written by 'subiquity'
network:
  ethernets:
    enp2s0:
      dhcp4: no
      addresses: [10.10.1.11/24]
      gateway4: 10.10.1.1
      nameservers:
              addresses: [10.10.1.1]
  version: 2
​
```

# 设置时区
```
修改配置文件
vi .profile
​
# 添加下面一行代码到最后
TZ='Asia/Shanghai'; export TZ
```

# 更新源
备份 sources.list
cd /etc/apt
sudo cp sources.list sources.list.bak

编辑 sources.list
添加如下内容：
```
# 阿里源
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
​```
