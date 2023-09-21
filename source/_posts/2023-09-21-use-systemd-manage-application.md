---
title: 为应用创建守护进程
comments: true
categories:
  - tech
tags: [systemd]
date: 2023-09-21 22:34:53
updated: 2023-09-21 22:34:53
---

本文介绍在 Linux 系统中，通过 systemd 来管理 Spring Boot 应用，实现当服务意外终止后自动重启。

# 目的

通过 systemd 管理 Spring Boot 应用，防止服务意外停止，增强可靠性。
可以实现开机自启并自动重启，意外停止自动重启等，守护线上应用。

# 操作步骤

## 1. 创建服务文件

在/etc/systemd/system目录下创建一个文件，如 moon.service，内容如下：

```text
[Unit]
Description=moon data center service

[Service]
ExecStart=/home/ethan/software/jdk1.8.0_171/bin/java -server -Xms256m -Xmx1024m -Dfile.encoding=utf-8 -jar /home/ethan/servers/moon-admin/moon-admin-1.0.0-SNAPSHOT.jar &
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

> 💡 Tips：Restart 参数可以设置为 always、on-failure。其中 always 表示程序异常退出时总是重启；on-failure 表示只在程序非正常退出时重启。

## 2.启动服务

`systemctl start moon`

> 💡 Tips：可以执行命令systemctl enable moon，来设定服务在开机启动。
 
# 其他

可以添加一些状态检查的脚本来确保应用已完全启动。另外还需要注意日志管理、资源分配等细节问题。
