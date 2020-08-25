---
title: spring-boot-with-docker
comments: true
categories:
  - 技术
tags: [docker,springboot]
date: 2020-08-22 21:52:20
updated: 2020-08-22 21:52:20
---

Docker is a Linux container management toolkit with a "social" aspect, allowing users to publish container images and consume those published by others. A Docker image is a recipe for running a containerized process, and in this guide we will build one for a simple Spring boot application.

# Containerize It
Docker has a simple "Dockerfile" file format that it uses to specify the "layers" of an image. So let’s go ahead and create a Dockerfile in our Spring Boot project:

Dockerfile
```
FROM openjdk:8-jdk-alpine
RUN addgroup -S ahshumei && adduser -S ethan -G ahshumei
USER ethan:ahshumei
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

Also, there is a clean separation between dependencies and application resources in a Spring Boot fat jar file, and we can use that fact to improve performance. The key is to create layers in the container filesystem. The layers are cached both at build time and at runtime (in most runtimes) so we want the most frequently changing resources, usually the class and static resources in the application itself, to be layered after the more slowly changing resources. Thus we will use a slightly different implementation of the Dockerfile:
```
FROM openjdk:8-jdk-alpine
MAINTAINER ethan@ahshumei.com
RUN addgroup -S ahshumei && adduser -S ethan -G ahshumei
USER ethan:ahshumei
ARG DEPENDENCY=target/dependency
COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY ${DEPENDENCY}/META-INF /app/META-INF
COPY ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","com.ahshumei.EurekaApplication"]
```
This Dockerfile has a DEPENDENCY parameter pointing to a directory where we have unpacked the fat jar. From a Maven build:
```
$ mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)
```

运行以下命令创建镜像（如果用的是Maven的话）：
```
$ docker build -t ahshumei/eureka-docker .
```
This command builds an image and tags it as ahshumei/eureka-docker.

# 创建自定义网络并设置固定IP
在搭建一些集群软件的时候，组件和组件之间需要进行网络通信，这个时候如果每次重启IP都发生变化会很不方便，因此希望能够将容器的IP固定下来，这也是可以实现的，具体参考下面的方法。
1.创建自定义网络
```
docker network create --subnet=172.18.0.0/16 gc-network
```
2.创建Docker容器
```
docker run --name eureka -p 10090:10090 --net=gc-network --ip=172.18.0.90 -t ahshumei/eureka-docker
```
使用docker inspect container-id可以看到当前容器分配的IP就是固定IP了。

# Build a Docker Image with Maven
To get started quickly, you can run the Spring Boot image generator without even changing your pom.xml (and remember the Dockerfile if it is still there is ignored):
```
$ ./mvnw spring-boot:build-image -Dspring-boot.build-image.imageName=springio/gs-spring-boot-docker
```
To push to a Docker registry you will need to have permission to push, which you won’t have by default. Change the image prefix to your own Dockerhub ID, and docker login to make sure you are authenticated before you run Docker.

# Using Spring Profiles
Running your freshly minted Docker image with Spring profiles is as easy as passing an environment variable to the Docker run command
```
$ docker run -e "SPRING_PROFILES_ACTIVE=prod" -p 10900:10900 -t ahshumei/eureka-docker
```
# Debugging the application in a Docker container
To debug the application JPDA Transport can be used. So we’ll treat the container like a remote server. To enable this feature pass a java agent settings in JAVA_OPTS variable and map agent’s port to localhost during a container run. With the Docker for Mac there is limitation due to that we can’t access container by IP without black magic usage.
```
$ docker run -e "JAVA_TOOL_OPTIONS=-agentlib:jdwp=transport=dt_socket,address=5005,server=y,suspend=n" -p 10900:10900 -t ahshumei/eureka-docker
```

https://spring.io/guides/gs/spring-boot-docker/
