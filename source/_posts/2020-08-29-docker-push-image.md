---
title: 发布 docker 镜像
comments: true
categories:
  - 工具
tags: [docker]
date: 2020-08-29 17:39:58
updated: 2020-08-29 17:39:58
---

## 发布镜像到 docker 私服
Dockerfile 示例：
```
# syntax=docker/dockerfile:experimental
FROM openjdk:8-jdk-alpine as build
WORKDIR /workspace/app
COPY target/bootstrap-1.0.0-SNAPSHOT.jar app.jar
RUN mkdir -p target/dependency && (cp app.jar target) && (cd target/dependency; jar -xf ../*.jar)

FROM openjdk:8-jdk-alpine
VOLUME /tmp
RUN addgroup -S ahshumei && adduser -S ethan -G ahshumei
USER ethan:ahshumei
ARG DEPENDENCY=/workspace/app/target/dependency
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","com.EmergencyManagementApplication"]
```
参考此示例时注意修改第四行jar包名称和最后一行的运行类名。

### 1.打包
```
mvn -U clean package -Dmaven.test.skip=true
或者指定打包模块命令：
mvn -U clean package -Dmaven.test.skip=true -pl 模块名称 -am
```

### --2.解压文件--
```
mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)
```

### 3.创建镜像
```
DOCKER_BUILDKIT=1 docker build -t ethan/镜像名 .
```

### --4.创建容器--
可在本地创建容器，用于测试镜像是否可正常使用。
指定端口、网络、以及配置文件创建并运行容器
docker run --name process -v /opt/docker_v/application-cloud.yml:/app/application-cloud.yml -p 10100:10100 --net=moon-network --ip=172.127.0.100 -t ethan/镜像名 --spring.profiles.active=cloud
指定端口创建并运行容器
docker run --name process1 -p 10100:10100 -t ethan/镜像名
将主机 /opt/docker_v/application-cloud.yml 文件挂载到容器的 /app/application-cloud.yml 文件
-v /opt/docker_v/application-cloud.yml:/app/application-cloud.yml

### 5.发布镜像
docker login -u ethan harbor私服IP:端口
docker tag 镜像ID harbor私服IP:端口/库名/镜像名
docker push harbor私服IP:端口/库名/镜像名

### 发布镜像到 docker hub
在docker hub 注册账号：https://hub.docker.com/repositories
使用Docker hub账号在验证本地登录：
```
docker login
```
修改 repository 名称，即 DockerID/仓库名，不一致将无法发布：
docker tag 镜像ID 用户名称/镜像源名(repository name):新的标签名(tag)
$ docker tag 487c260f20ee ethan2docker/moon
docker push <your_username>/my-first-repo:tag
$ docker push ethan2docker/moon
