---
title: 发送 crontab 定时任务执行结果到指定邮箱
comments: true
categories:
  - 技术
tags: [crontab,mail]
date: 2023-05-27 20:32:14
updated: 2023-05-27 20:32:14
---

在 Ubuntu 中，可以通过 crontab 服务的邮件功能，将定时任务的执行结果发送到指定邮箱。具体步骤如下：

1. 安装 mailutils

```shell
bash 
sudo apt install mailutils
```

配置过程需要

- 选择配置类型，选择 `No configuration`。

安装日志中有 postfix 的配置说明：

Postfix was not set up.  Start with
  cp /usr/share/postfix/main.cf.debian /etc/postfix/main.cf
.  If you need to make changes, edit
/etc/postfix/main.cf (and others) as needed.  To view Postfix configuration
values, see postconf(1).

After modifying main.cf, be sure to run '/etc/init.d/postfix reload'.

mailutils提供用于 crontab 发送邮件的环境。

2. 设置邮件接收者：

编辑crontab文件:

```shell
crontab -e 
```

在文件末尾添加:

```
MAILTO="your_email@example.com"
```

将`[your_email@example.com](mailto:your_email@example.com)`替换为您要接收邮件的邮箱地址。

3. 设置邮件发送环境变量
   执行以下命令设置发送邮箱、SMTP服务器和端口:

```shell
bash
export MAIL="your_email@example.com"   # 替换为接收邮箱地址
export SMTP="smtp.example.com"         # 替换为SMTP服务器地址 
export PORT=25                         # 设置SMTP端口,一般为25或587
```

这会设置邮箱地址、SMTP服务器和SMTP端口,用于发送邮件。

4. 编写任务脚本，在脚本中判断任务是否执行成功,如果失败则发送邮件。例如：

```shell
bash
#!/bin/bash

# 执行任务
some_command

# 判断任务执行结果
if [ $? -ne 0 ]; then
    # 发送邮件,邮件内容为任务输出
    echo "Subject: Task Failed" | mail -s "Task Failed" $MAIL 
fi 
```

\- `some_command` 替换为要执行的命令
\- `$?` 用于获取上一条命令的返回值,如果不等于 0 表示失败
\- `mail` 命令用于发送邮件
\- `-s` 指定邮件主题
\- `$MAIL` 为预先配置的接收邮箱环境变量3. 为任务脚本添加可执行权限:`chmod +x script.sh`4. 在crontab中添加定时任务来执行该脚本:

5. 添加定时任务在 crontab 文件中添加定时任务，例如:

```shell
*/5 * * * * /path/to/script.sh   # 每5分钟执行一次
```

这样，当定时任务执行失败时，会发送一封邮件通知到您指定的邮箱，实现了只在任务失败时发送邮件的效果。

