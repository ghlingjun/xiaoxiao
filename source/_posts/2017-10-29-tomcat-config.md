---
title: Tomcat 配置
date: 2017-10-29 20:50:15
tags: tomcat
---

1. 注释 ajp 协议
2. 修改 HTTP/1.1（bio） 协议
修改 HTTP/1.1（bio） 协议为 org.apache.coyote.http11.Http11NioProtocol （nio）协议；使用 apr 性能会更高；
3. 开启线程池
开启线程池；线程池默认数量为 200，可根据服务器性能调整数量；
