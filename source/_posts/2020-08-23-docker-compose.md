---
title: docker compose 服务编排
comments: true
categories:
  - 技术
tags: [docker]
date: 2020-08-23 22:07:06
updated: 2020-08-23 22:07:06
---

安装 docker-compose
```
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
验证：
docker-compose -version
PS：如果想要卸载docker-compose，请执行以下命令
sudo rm /usr/local/bin/docker-compose


docker-compose 配置示例
```
version: '3'
services:
  gc-admin:
    build: ./spring-boot-admin
    volumes:
      - "/opt/docker_v/application-cloud.yml:/app/application-cloud.yml"
    ports:
      - "10091:10091"
    networks:
      gcnet:
        ipv4_address: 172.127.0.91
    environment:
      spring.profiles.active: cloud
#  redis:
#    image: "redis:alpine"
  networks:
    gcnet:
      external:
        name: gc-network
```

### 构建
```
docker-compose build
```

### 启动
```
docker-compose up -d

docker-compose ps
```

The docker-compose run command allows you to run one-off commands for your services. For example, to see what environment variables are available to the web service:
$ docker-compose run web env

docker-compose stop
You can bring everything down, removing the containers entirely, with the down command. Pass --volumes to also remove the data volume used by the Redis container:
$ docker-compose down --volumes

docker-compose rm --force

### 遇到问题
1. http: server gave HTTP response to HTTPS client
解决办法：
在 /etc/docker 下，创建 daemon.json 文件，写入：
```
{
  "debug": true,
  "experimental": false,
  "registry-mirrors": [
    "http://hub-mirror.c.163.com",
    "https://d4u3cjo2.mirror.aliyuncs.com"
  ],
  "insecure-registries": [
    "IP:PORT" harbor私服的IP和端口
  ]
}
```
重启 docker：
```
systemctl restart docker.service
sudo service docker restart
```
docker启动日志：
```
/var/log/upstart/docker.log
```

# 参考文档
Get started with Docker Compose[https://docs.docker.com/compose/gettingstarted/]
