---
title: Nginx Command Instructions
date: 2017-09-17 22:19:40
categories:
  - 工具
tags: [nginx]
---
# 概述
Nginx (engine x) 是一个高性能的 HTTP 和反向代理服务器，也是一个 IMAP/POP3/SMTP 服务器。Nginx 是由伊戈尔·赛索耶夫为俄罗斯访问量第二的 Rambler.ru 站点开发的，第一个公开版本 0.1.0 发布于 2004 年 10 月 4 日。

## nginx 安装(Ubuntu 系统)
```
sudo apt-get install nginx
```
The configuration file of nginx is named nginx.conf and placed in the directory /etc/nginx
## 启动 nginx 服务
To start nginx, run the executable file. 
```
sudo nginx
```
## nginx commands
Once nginx is started, it can be controlled by invoking the executable with the `-s` parameter. Use the following syntax:
```
nginx -s signal
```
Where *signal* may be one of the following:
- `stop` — fast shutdown
- `quit` — graceful shutdown
- `reload` — reloading the configuration file
- `reopen` — reopening the log files

For getting the list of all running nginx processes, the `ps` utility may be used, for example, in the following way:
```
ps -ax | grep nginx
```

# Nginx 配置

[参考]https://www.cnblogs.com/lidabo/p/4169396.html
