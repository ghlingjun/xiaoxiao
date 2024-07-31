---
title: 记一次 ssh 版本升级步骤
comments: true
categories:
  - 运维
tags: [ssh]
date: 2024-07-02 19:10:54
updated: 2024-07-02 19:10:54
---

# 验证 ssh 依赖软件版本
首先查看 openSSH 的安装说明，确定 openssl、zlib 的最低版本要求。例如 openssh-9.7p1 的要求如下：
```text
A working installation of zlib:
Zlib 1.1.4 or 1.2.1.2 or greater (earlier 1.2.x versions have problems):
https://zlib.net/

libcrypto from either of LibreSSL or OpenSSL.  Building without libcrypto
is supported but severely restricts the available ciphers and algorithms.
 - LibreSSL (https://www.libressl.org/) 3.1.0 or greater
 - OpenSSL (https://www.openssl.org) 1.1.1 or greater

LibreSSL/OpenSSL should be compiled as a position-independent library
(i.e. -fPIC, eg by configuring OpenSSL as "./config [options] -fPIC"
or LibreSSL as "CFLAGS=-fPIC ./configure") otherwise OpenSSH will not
be able to link with it.  If you must use a non-position-independent
libcrypto, then you may need to configure OpenSSH --without-pie.

If you build either from source, running the OpenSSL self-test ("make
tests") or the LibreSSL equivalent ("make check") and ensuring that all
tests pass is strongly recommended.

NB. If you operating system supports /dev/random, you should configure
libcrypto (LibreSSL/OpenSSL) to use it. OpenSSH relies on libcrypto's
direct support of /dev/random, or failing that, either prngd or egd.
```
从 openSSH 的安装说明中，可以得知，libcrypto 的最低版本要求是 LibreSSL 3.1.0 或 OpenSSL 1.1.1 或更高版本。
查看服务器上的 openssl 和 zlib 版本：
```shell
openssl version
OpenSSL 1.1.1v  1 Aug 2023

find /usr/ -name zlib.pc
/usr/lib/x86_64-linux-gnu/pkgconfig/zlib.pc
/usr/local/lib/pkgconfig/zlib.pc
/usr/local/zlib/lib/pkgconfig/zlib.pc

cat /usr/lib/x86_64-linux-gnu/pkgconfig/zlib.pc
prefix=/usr
exec_prefix=${prefix}
libdir=${prefix}/lib/x86_64-linux-gnu
sharedlibdir=${libdir}
includedir=${prefix}/include

Name: zlib
Description: zlib compression library
Version: 1.2.11

Requires:
Libs: -L${libdir} -L${sharedlibdir} -lz
Cflags: -I${includedir}
```
检查是否有 gcc 编译器：
```shell
gcc --version
# 如果没有的话安装
apt-get install build-essential
```
# 下载安装包
openssh-9.7p1.tar.gz 下载
https://mirrors.aliyun.com/pub/OpenBSD/OpenSSH/portable/openssh-9.7p1.tar.gz
openssl-1.1.1w.tar.gz 下载
https://www.openssl.org/source/old/1.1.1/openssl-1.1.1w.tar.gz
zlib 最新版本下载
https://zlib.net/current/zlib.tar.gz

```shell
# 上传到服务器
scp openssh-9.7p1.tar.gz ethan@ip:/home/ethan/soft/
```

# 安装 openSSH
## 备份 openSSH
```shell
mkdir ~/backup/ssh/bak240328
ls /etc/ssh
sudo mv /etc/ssh/* ~/backup/ssh/bak240328

mkdir ~/backup/ssh/pamd240328
ls /etc/pam.d/sshd
sudo mv /etc/pam.d/sshd* ~/backup/ssh/pamd240328

mkdir ~/backup/ssh/etcinitdbak240328
ls /etc/init.d/ssh*
sudo mv /etc/init.d/ssh* ~/backup/ssh/etcinitdbak240328/

ls /usr/bin/ssh*
sudo mv /usr/bin/ssh* ~/backup/ssh/

# 停止 openSSH 服务
sudo systemctl sshd.service stop
```
## 卸载 openSSH
```shell
sudo apt-get remove openssh-server openssh-client -y
# or
sudo apt purge --remove "openssh*"
```
## 安装 openSSH
```shell
tar -xvzf openssh-9.7p1.tar.gz
cd openssh-9.7p1
# 编译配置
./configure --prefix=/usr/local/openssh --sysconfdir=/etc/ssh  --with-ssl-dir=/usr/local/openssl --with-zlib-dir=/usr/local/zlib --without-openssl-header-check
# 编译
make
# 安装
sudo make install

sudo ln -s /usr/local/openssh/bin/ssh /usr/local/bin/ssh
ssh -V
```
## 将 openSSH 注册为服务
```shell
sudo vi /etc/systemd/system/sshd.service
# /usr/lib/systemd/system/sshd.service

# 添加以下内容：
[Unit]
Description=OpenSSH server
Documentation=man:sshd(8) man:sshd_config(5)
#After=network.target sshd-keygen.service
#Wants=sshd-keygen.service
After=network.target

[Service]
#Type=notify
#EnvironmentFile=/etc/sysconfig/sshd
#ExecStart=/usr/local/openssh/sbin/sshd -D $OPTIONS
ExecStart=/usr/local/openssh/sbin/sshd
#ExecReload=/bin/kill -HUP $MAINPID
#KillMode=process
#Restart=on-failure
#RestartSec=42s

[Install]
WantedBy=multi-user.target
```
## 重载 Systemctl, 并设置为自启动
```shell
sudo systemctl enable sshd
sudo systemctl daemon-reload
sudo systemctl start sshd.service
```
## 检查服务状态
```shell
systemctl status sshd
netstat -anpt | grep 22
```
# 其他
```shell
# 手动启动 openSSH
sudo /usr/local/openssh/sbin/sshd

# 测试 scp 命令
scp zlib.tar.gz ethan@ip:/home/ethan/
```
## 注册服务失败
主要原因是 sshd.service 或者 ssh.service 冲突导致，主要查看以下两个目录：
```shell
ls /etc/systemd/system/ssh*
ls /usr/lib/systemd/system/ssh*
```
## 参考文档
https://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/INSTALL
https://www.cnblogs.com/tangllty/p/18054446
https://blog.csdn.net/chsh4587/article/details/136328617
