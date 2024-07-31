---
title: Ubuntu 安装 SFTP 服务
comments: true
categories:
  - 运维
tags: [sftp]
date: 2021-11-23 21:44:47
updated: 2021-11-23 21:44:47
---
# 目标

在 Ubuntu 系统上开通 sftp 文件服务，允许指定用户上传及下载文件。但是这些用户只能使用 sftp 传输文件，不能使用 SSH 终端访问服务器，并且 sftp 不能访问系统文件。系统管理员则既能使用 sftp 传输文件，也能使用 SSH 远程管理服务器。
以下是将允许 sftp 用户组内的用户使用 sftp，但不允许使用 SSH Shell，且该组用户不能访问系统文件。

# 安装步骤

## 查看是否已安装 sftp

```shell
dpkg --get-selections | grep ssh
```

## 新建 sftp 用户组

```shell
# 查看用户组是否存在
grep sftp /etc/group
# 添加用户组 sftp
groupadd sftp
```

## 新建 sftp 用户

```shell
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

```shell
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

`sudo vim /etc/ssh/sshd_config`

搜索以端口22开头的行。通常，该行使用井号(＃)注释掉。 删除哈希号，然后输入新的SSH端口号： Port 2222

编辑配置文件时要非常小心。 错误的配置可能会阻止SSH服务启动。

完成后，保存文件并重新启动SSH服务以使更改生效： `sudo systemctl restart ssh`

# 常用命令

访问 sftp 服务：

```shell
sftp -P 2222 sftp@127.0.0.1

# 上传一个文件
put local_file remote_file
put /home/user/test.txt /test/test_upload.txt

# 下载一个文件
get remote_file local_file
get /test/test.txt ~/Downloads/download.txt
```

# 使用 SSH 密钥对来访问 sftp 服务

首先为 sftp 用户创建 SSH 密钥对，然后将公钥添加到 sftp 用户的 SSH 允许列表中。这样 sftp 用户就可以使用 SSH 密钥对来访问 sftp 服务了。

## 第一步查看已存在的密钥对
 
先检查是否已存在 SSH 密钥，SSH 密钥对一般存放在本地用户的根目录下。已存在本地公钥，你可以跳过生成 SSH 密钥。

ED25519 算法
```shell
cat ~/.ssh/id_ed25519.pub
```

RSA 算法
```shell
cat ~/.ssh/id_rsa.pub
```

如果返回一长串以 ssh-ed25519 或 ssh-rsa 开头的字符串, 说明已存在本地公钥，你可以跳过生成 SSH 密钥。如果不存在，参考第二步生成 SSH 密钥对。

## 第二步生成 SSH 密钥对

SSH 加密算法类型有 ED25519、RSA 等。

ED25519 是一种基于椭圆曲线密码学的数字签名算法，属于 EdDSA 签名方案的一部分。它使用 SHA-512/256 散列函数和 Curve25519 椭圆曲线。由于其数学特性，ED25519 被认为是目前最安全、加解密速度最快的密钥类型之一。它的密钥长度比 RSA 小很多，因此具有更好的性能。然而，由于它的新颖性和复杂性，一些旧的软件或系统可能不支持 ED25519。

RSA 是一种广泛使用的公钥加密算法，既可以用于数据加密，也可以用于数字签名。RSA 的安全性基于大质数的难以分解性质。然而，随着计算机技术的发展，RSA 的安全性可能会受到威胁。RSA 密钥的长度通常比 ED25519 长，因此加解密速度相对较慢。但是，由于 RSA 的广泛使用和支持，它仍然是最兼容的密钥类型之一。

推荐使用 ED25519 算法生成 SSH 密钥对：

```shell
# 注释会出现在.pub文件中，一般可使用邮箱作为注释内容，或者使用用户名作为注释内容
ssh-keygen -t ed25519 -C "<注释内容>"
```

基于 RSA 算法，生成密钥对命令如下：

```shell
ssh-keygen -t rsa -C "<注释内容>"
```

> 警告：密钥用于鉴权，请谨慎保管。公钥文件以 .pub 扩展名结尾，可以公开给其他人，而没有 .pub 扩展名的私钥文件不要泄露给任何人！

## 第三步复制公钥

查看公钥，然后复制到剪贴板

```shell
# ED25519 算法
cat ~/.ssh/id_ed25519.pub
# 如果使用 RSA 算法生成密钥对，使用以下命令
cat ~/.ssh/id_rsa.pub
```

## 第四步添加公钥

将公钥添加到 sftp 用户的 SSH 允许列表中：

```shell
# 添加公钥
vi /home/sftp/.ssh/authorized_keys
```

## 测试确认

测试确认是否可以使用 SSH 密钥对来访问 sftp 服务：
```shell
sftp -P 2222 sftpuser@ip
```
如果登陆成功，说明可以使用 SSH 密钥对来访问 sftp 服务了。
