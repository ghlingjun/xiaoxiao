---
title: Tomcat 配置
date: 2017-10-29 20:50:15
categories:
  - 技术
tags: [tomcat]
---

1. 注释 ajp 协议
2. 修改 HTTP/1.1（bio） 协议
修改 HTTP/1.1（bio） 协议为 org.apache.coyote.http11.Http11NioProtocol （nio）协议；使用 apr 性能会更高；
```
<Connector executor="tomcatThreadPool" port="8080" protocol="org.apache.coyote.http11.Http11Nio2Protocol"
       connectionTimeout="20000" redirectPort="8443"
       enableLookups="false" maxPostSize="10485760" URIEncoding="UTF-8" acceptCount="100"
       acceptorThreadCount="2" disableUploadTimeout="true" maxConnections="10000" SSLEnabled="false"/>
```
3. 开启线程池
开启线程池；线程池默认数量为 200，可根据服务器性能调整数量；
```
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"  maxThreads="420" minSpareThreads="4"/>
```
4. tomcat 8 以上对 resource 采取了 cache，默认的大小是 10240(10M)，修改其默认值
```
<Resources cachingAllowed="true" cacheMaxSize="102400" />
```
