---
title: Ubuntu 安装 SFTP 服务
comments: true
categories:
  - 技术
tags: [sftp]
date: 2021-11-23 15:44:47
updated: 2021-11-23 15:44:47
---

# 目标
在 Ubuntu 系统上开通 sftp 文件服务，允许指定用户上传及下载文件。但是这些用户只能使用 sftp 传输文件，不能使用 SSH 终端访问服务器，并且 sftp 不能访问系统文件。系统管理员则既能使用 sftp 传输文件，也能使用 SSH 远程管理服务器。
以下是将允许 sftp 用户组内的用户使用 sftp，但不允许使用 SSH Shell，且该组用户不能访问系统文件。

# 安装步骤
## 查看是否已安装 sftp
dpkg --get-selections | grep ssh

## 新建 sftp 用户组
```
# 查看用户组是否存在
grep sftp /etc/group
# 添加用户组 sftp
groupadd sftp
```

## 新建 sftp 用户
```
# 查看用户 sftp 是否存在
grep sftp /etc/passwd
# 添加用户
useradd -g sftp -m sftp
```

将 sftp 从所有其他用户组中移除并加入到 sftp 组，并且关闭其 Shell 访问：
sudo usermod -G sftp -s /bin/false sftp

## 创建并设置 sftp 用户目录
准备“监狱”的根目录及共享目录，“监狱”的根目录必须满足以下要求：
所有者为 root，其他任何用户都不能拥有写入权限。
因此，为了让 sftp 用户能够上传文件，还必须在“监狱”根目录下再创建一个普通用户能够写入的共享文件目录。
```
sudo mkdir /home/sftp
sudo mkdir /home/sftp/shared
sudo chown shumei:sftp /home/sftp/shared
sudo chmod 770 /home/sftp/shared
```

## 修改SSH配置文件
```
vi  /etc/ssh/sshd_config
注释内容：
#Subsystem      sftp    /usr/libexec/openssh/sftp-server
在文件的最后，添加以下内容：
Subsystem       sftp    internal-sftp
AllowGroups shumei sftp
Match Group sftp
ChrootDirectory /home/sftp
AllowTcpForwarding no
X11Forwarding no
ForceCommand internal-sftp -d shared
```

这些内容的意思是：
只允许 shumei 及 sftp 通过SSH访问系统；
针对 sftp 用户，额外增加一些设置：
将“/home/sftp”设置为该组用户的系统根目录（因此它们将不能访问该目录之外的其他系统文件）；
禁止 TCP Forwarding 和X11 Forwarding；
强制该组用户仅仅使用 SFTP，“-d shared”默认用户登陆后自动进入 shared 目录。
如果需要进一步了解细节，可以使用“man sshd_config”命令。这样设置之后，SSH用户组可以访问SSH，并且不受其他限制；而SFTP用户组仅能使用SFTP进行访问，而且被关进监狱目录。

## 设置 sftp 端口
sudo vim /etc/ssh/sshd_config
搜索以端口22开头的行。通常，该行使用井号(＃)注释掉。 删除哈希号，然后输入新的SSH端口号：
Port 2222
编辑配置文件时要非常小心。 错误的配置可能会阻止SSH服务启动。
完成后，保存文件并重新启动SSH服务以使更改生效：
sudo systemctl restart ssh
