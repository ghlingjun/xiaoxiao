---
title: Ubuntu Server 安装
comments: true
categories:
  - 运维
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
```

# 设置时区
```
修改配置文件
vi .profile

# 添加下面一行代码到最后
TZ='Asia/Shanghai'; export TZ
```

# 更新源
备份 sources.list
cd /etc/apt
sudo cp sources.list sources.list.bak

编辑 sources.list
添加如下内容（选择一个源即可）：
```
# tsinghua
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team, and may not be under a free licence. Please satisfy yourself as to
## your rights to use the software. Also, please note that software in
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted
deb http://security.ubuntu.com/ubuntu/ jammy-security universe
deb http://security.ubuntu.com/ubuntu/ jammy-security multiverse

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
```
