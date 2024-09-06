---
title: 使用 rsync 服务备份 nas 数据
comments: true
categories:
  - 运维
  - nas
tags:
  - nas
  - rsync
date: 2024-09-05 15:18:31
updated: 2024-09-05 15:18:31
---

Nas 中的数据可通过 Hyper Backup 进行备份，Hyper Backup 可保留最多达 65,535 个版本的数据，同时通过跨版本重复数据删除功能，使存储空间消耗最小化。
备份的数据保留在一个拥有专利的数据库中，该数据库可通过 DSM、Windows 和 Linux 平台上专门设计的多版本资源管理器来浏览、下载或还原。
通过 Hyper Backup，可以将数据备份到本地/远程 Synology NAS 设备，备份到远程 rsync、WebDav 和 OpenStack 服务器，备份到公有云。
这里介绍如何将数据备份到远程 rsync 服务器。

# 首选需要配置 rsync 服务器
## rsync 简介
Rsync(remote synchronize) 是一个常用的 Linux 应用程序，用于文件同步。它可以同步本地和远程主机之间的文件。
与 FTP 或 scp 等其他文件传输工具不同，其最大的特点是：
会检查发送方和接收方已有的文件，仅传输有变动的部分（默认规则是文件大小或修改时间有变动）。

## 安装
ubuntu 默认安装了 rsync。若备份服务器没有安装 rsync，可以用下面的命令安装。
```bash
# Debain/Ubuntu
sudo apt install rsync

# CentOS
sudo yum install rsync
```
## 配置
Ubuntu 默认的配置文件位置：/usr/share/doc/rsync/examples/rsyncd.conf
需要将其复制到 /etc/ 目录下。
配置示例：
```text
# GLOBAL OPTIONS
#motd file=/etc/motd
log file=/var/log/rsyncd
# for pid file, do not use /var/run/rsync.pid if
# you are going to run rsync out of the init.d script.
# The init.d script does its own pid file handling,
# so omit the "pid file" line completely in that case.
pid file=/var/run/rsyncd.pid
syslog facility=daemon

# MODULE OPTIONS
[module_name]
        comment = public archive
        path = /home/username/backups/nas
        use chroot = no
        lock file = /var/lock/rsyncd

        read only = no
        list = yes
        uid = username
        gid = groupname

        auth users = nas_user
        secrets file = /etc/rsyncd.secrets
        strict modes = yes
        hosts allow = 10.10.1.190 10.10.1.189
        
        ignore errors = yes
        ignore nonreadable = yes
        transfer logging = yes

        timeout = 600
        refuse options = checksum dry-run
        dont compress = *.gz *.tgz *.zip *.z *.rpm *.deb *.iso *.bz2 *.tbz
```
示例配置使用了验证权限的配置，需要建立：
/etc/rsyncd.secrets
```text
nas_user:nas_password
```
修改 rsyncd.secrets 访问权限为 600。

## 启动 rsync 服务
配置完成后，启动 rsync 服务。
```bash
sudo /etc/init.d/rsync start
```
可在　/etc/default 路径下的 rsync 文件中将其改为自启动：
`RSYNC_ENABLE=true`

## 测试
```shell
rsync -vzrtopg nas_user@10.10.1.9::module_name /home/shumei/backups/nas
rsync -avz /mnt/d/honor.csv nas_user@10.10.1.9::module_name
rsync -avz /mnt/d/honor.csv nas_user@10.10.1.9::module_name --password-file=/etc/rsyncd.secrets
```

## 其次配置 Hyper Backup
- 从 nas 套件中心安装 Hyper Backup 套件。
- 打开新增备份任务向导，备份类型选择【文件夹和套件】，下一步。
- 备份目的地选择文件服务器中的【rsync】，下一步。
- 备份版本类型选择【多个版本】，下一步。
- 备份目的地设置：填写 ip、端口（默认 873）、用户名、密码、选择共享文件夹，然后下一步。
- 选择要备份的目录，然后点击【下一步】。
- 配置备份计划（每天运行一次；启用完整性检查，每周一次），然后点击【下一步】。
- 启用备份循环（Smart Recycle），保留版本的数量上限设置为 256，然后点击【完成】。

# 参考资料
https://kb.synology.cn/zh-cn/DSM/help/HyperBackup/data_backup_source?version=7
https://www.cnblogs.com/felixzh/p/4950049.html
https://www.ruanyifeng.com/blog/2020/08/rsync.html
