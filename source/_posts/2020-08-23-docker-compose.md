---
title: docker compose 服务编排
comments: true
categories:
  - 技术
tags: [docker]
date: 2020-08-23 22:07:06
updated: 2020-08-23 22:07:06
---

docker-compose 配置示例
```
version: '3'
services:
  gc-admin:
    build: ./spring-boot-admin
    ports:
      - "10091:10091"
    networks:
      gcnet:
        ipv4_address: 172.18.0.91
#  redis:
#    image: "redis:alpine"
  networks:
    gcnet:
      external:
        name: gc-network
```

### 构建
docker-compose build

### 启动
docker-compose up -d

docker-compose ps

The docker-compose run command allows you to run one-off commands for your services. For example, to see what environment variables are available to the web service:
$ docker-compose run web env

docker-compose stop
You can bring everything down, removing the containers entirely, with the down command. Pass --volumes to also remove the data volume used by the Redis container:
$ docker-compose down --volumes

docker-compose rm --force
