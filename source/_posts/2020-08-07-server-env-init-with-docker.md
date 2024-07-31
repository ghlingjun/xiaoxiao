---
title: 通过 docker 初始化服务器运行环境
comments: true
categories:
  - 工具
tags: [docker]
date: 2020-08-07 16:07:19
updated: 2020-08-07 16:07:19
---

使用官方安装脚本自动安装
安装命令如下：
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

如果要使用 Docker 作为非 root 用户，则应考虑使用类似以下方式将用户添加到 docker 组：
> sudo usermod -aG docker ethan

# 启动docker
sudo systemctl start docker

# 查看本地镜像
docker images
## 删除镜像
docker rmi hello-world

# 查看所有容器
docker ps -a
## 删除容器
docker rm -f 1e560fca3906

# 容器起停命令
docker stop 2b50eae281cb
docker start 2b50eae281cb
docker restart 2b50eae281cb

## MySql 部署
docker pull mysql:5.7

docker run -p 3306:3306 --name mysqlpro \
-v /home/shumei/docker/mysql/log:/var/log/mysql \
-v /home/shumei/docker/mysql/data:/var/lib/mysql \
-v /home/shumei/docker/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=密码 \
-d mysql:5.7

### 进入容器
docker exec -it mysqlpro bash

### 登录mysql
mysql -u root -p
create user 'shumei'@'%' identified by '密码';

create database fund_management default character set utf8;
grant all privileges on fund_management.* to shumei@'%' with grant option;
flush privileges;

## Redis 部署
docker pull redis

docker run -p 6379:6379 --name redis -v /home/shumei/docker/redis/data:/data \
-v /home/shumei/docker/redis/redis.conf:/etc/redis/redis.conf \
-v /home/shumei/docker/redis/data:/data \
-d redis redis-server /etc/redis/redis.conf --appendonly yes --requirepass "密码"

docker exec -it redis /bin/bash

## Nginx 部署
docker run -p 10000:10000 --name smnginx -v $PWD/www:/www -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf -v $PWD/logs:/wwwlogs -d nginx

参考文档：
https://www.runoob.com/docker/ubuntu-docker-install.html

## 遇到问题
重启 docker：
systemctl restart docker.service
sudo service docker restart
docker启动日志：
/var/log/upstart/docker.log
