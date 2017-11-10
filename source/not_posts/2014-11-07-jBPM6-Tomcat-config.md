---
layout: default
title: jBPM6 Tomcat 数据源配置
date: 2014-11-07 09:41:42
---

# jBPM6 Tomcat 数据源配置

context.xml 的WatchResource标签后中加入如下配置：
```
<Transaction factory="bitronix.tm.BitronixUserTransactionObjectFactory" />
    <Resource name="jdbc/jbpm-ds" auth="Container" type="javax.sql.DataSource"
              maxActive="15" maxIdle="2" maxWait="10000" logAbandoned="true"
              username="jbpm" password="jbpm"
              driverClassName="com.mysql.jdbc.Driver"
              url="jdbc:mysql://192.168.3.127:3306/jbpm"/>
```
web 项目中 web.xml 中加入配置：
```
<resource-env-ref>
    <resource-env-ref-name>jdbc/jbpm-ds</resource-env-ref-name> 
    <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type> 
</resource-env-ref>
```

# Could not find datasource: jdbc/jbpm-ds

Which datasource are you using?  
Are you using jdbc/jbpm-ds as datasource and it is not found? 
Also if you are  using tocmat you need to use java:comp/env/jdbc/jbpm-ds.
