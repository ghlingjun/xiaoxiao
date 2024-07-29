---
title: 发送定时任务执行结果到指定邮箱
comments: true
categories:
  - 运维
  - mail
tags:
  - ssmtp
  - mailutils
date: 2023-05-27 20:32:14
updated: 2023-07-18 20:32:14
---

在 Ubuntu 中，可以通过 crontab 服务的邮件功能，将定时任务的执行结果发送到指定邮箱。环境搭建方法如下：

# 1 安装配置环境

## 1.1 安装 ssmtp 和 mailutils

```shell
bash 
sudo apt install ssmtp mailutils
```

配置过程需要选择配置类型，选择 `No configuration`。

安装好后需要配置两个文件：`/etc/ssmtp/ssmtp.conf /etc/ssmtp/revaliases`

## 1.2 修改配置

### 修改 ssmtp 配置文件

设置发送邮箱、SMTP服务器和端口等：

`vi /etc/ssmtp/ssmtp.conf`

```shell
# Config file for sSMTP sendmail
#
# The person who gets all mail for userids < 1000
# Make this empty to disable rewriting.
root=postmaster

# The place where the mail goes. The actual machine name is required no
# MX records are consulted. Commonly mailhosts are named mail.domain.com
#mailhub=smtp.aliyun.com:465
mailhub=smtp.163.com:465

# The full hostname
hostname=ubuntu
UseTLS=Yes
# Are users allowed to set their own From: address?
# YES - Allow the user to specify their own From: address
# NO - Use the system generated From: address
FromLineOverride=NO

#邮箱名
AuthUser=your_email@163.com
#授权码
AuthPass=authpassasdfasdf

```

其中授权码 AuthPass 需要登录邮箱客户端获取。

### 修改配置文件 revaliases

`vi /etc/ssmtp/revaliases`

```shell
# sSMTP aliases
#
# Format:       local_account:outgoing_address:mailhub
#
# Example: root:your_login@your.domain:mailhub.your.domain[:port]
# where [:port] is an optional port number that defaults to 25.
# shumei 请修改为服务器用户名
shumei:your_email@163.com:smtp.163.com:465
```

至此邮件发送的环境已搭建完成。下面是编写脚本测试发送邮件。

# 2 测试邮件发送

## 2.1 编写任务脚本

在脚本中判断任务是否执行成功，如果失败则发送邮件。例如：

```shell
bash
#!/bin/bash

# 执行任务
some_command

# 判断任务执行结果
if [ $? -ne 0 ]; then
    # 发送邮件,邮件内容为任务输出
    echo "Subject: Task Failed" | mail -s "Task Failed" mailname@domain.com
fi 
```

\- `some_command` 替换为要执行的命令
\- `$?` 用于获取上一条命令的返回值,如果不等于 0 表示失败
\- `mail` 命令用于发送邮件
\- `-s` 指定邮件主题
\- `$MAIL` 为预先配置的接收邮箱环境变量

完成后为任务脚本添加可执行权限:`chmod +x script.sh`

## 2.2 添加定时任务

在 crontab 文件中添加定时任务，例如:

```shell
*/5 * * * * sh /path/to/script.sh   # 每5分钟执行一次
```

当定时任务执行失败时，会发送一封邮件通知到您指定的邮箱，实现了只在任务失败时发送邮件的效果。

# 参考文档

https://blog.csdn.net/gmaaa123/article/details/137954791
https://devpress.csdn.net/linux/62eba51020df032da732ba45.html
