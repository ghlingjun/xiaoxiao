---
title: jasypt spring boot
comments: true
categories:
  - 技术
tags: [jasypt]
date: 2023-07-14 18:34:53
updated: 2023-07-14 18:34:53
---

本文介绍如何将 jasypt 集成进 spring boot 项目中，用于解决用户、密码等敏感信息在代码中明文存储的问题。

# 什么是 jasypt spring boot

**[Jasypt](http://www.jasypt.org/)**  is a java library which allows the developer to add basic encryption capabilities to his/her projects with minimum effort, and without the need of having deep knowledge on how cryptography works.

Jasypt 的特点请参看：http://www.jasypt.org/features.html

# 集成方法

**[开源项目文档](https://github.com/ulisesbocchio/jasypt-spring-boot)** 中介绍了三种集成方式，本文仅详细介绍第一种详细集成方式（本文只做参考，应以开源项目文档未准）。

## 三种集成方式

### 第一种

如果项目使用`@SpringBootApplication` 或者 `@EnableAutoConfiguration`，直接添加 jar 依赖即可。加密的配置项会在整个 Spring 项目中生效（This means any system property, environment property, command line argument, application.properties, application-*.properties, yaml properties, and any other property sources can contain encrypted properties）。

```xml
<dependency>
        <groupId>com.github.ulisesbocchio</groupId>
        <artifactId>jasypt-spring-boot-starter</artifactId>
        <version>3.0.5</version>
</dependency>
```

### 第二种

如何项目中不使用自动配置注解 `@SpringBootApplication` 或`@EnableAutoConfiguration` ，那么添加如下依赖到项目中：

```xml
<dependency>
        <groupId>com.github.ulisesbocchio</groupId>
        <artifactId>jasypt-spring-boot</artifactId>
        <version>3.0.5</version>
</dependency>
```

再添加 `@EnableEncryptableProperties` 到你的 Configuration class. 示例：

```java
@Configuration
@EnableEncryptableProperties
public class MyApplication {
    ...
}
```

这样加密配置项就会在整个 Spring 项目中生效（This means any system property, environment property, command line argument, application.properties, yaml properties, and any other custom property sources can contain encrypted properties）。

### 第三种

如果项目中不使用自动装配注解 `@SpringBootApplication` or `@EnableAutoConfiguration` ，并且你也不想在整个 Spring 项目中启用加密配置，有第三种配置方式可以满足。首先添加下面依赖到项目中：

```xml
<dependency>
        <groupId>com.github.ulisesbocchio</groupId>
        <artifactId>jasypt-spring-boot</artifactId>
        <version>3.0.5</version>
</dependency>
```

然后在你的配置文件中根据需要添加注解`@EncryptablePropertySource`，就像添加 Spring 的注解`@PropertySource`一样，例如：

```java
@Configuration
@EncryptablePropertySource(name = "EncryptedProperties", value = "classpath:encrypted.properties")
public class MyApplication {
	...
}
```

还有一个注解`@EncryptablePropertySources`，用于添加一组`@EncryptablePropertySource`注解，例如：

```java
	@Configuration
	@EncryptablePropertySources({@EncryptablePropertySource("classpath:encrypted.properties"),
	                             @EncryptablePropertySource("classpath:encrypted2.properties")})
	public class MyApplication {
		...
	}
```

Also, note that as of version 1.8, `@EncryptablePropertySource` supports YAML files.

## 集成详细步骤

### 1. 添加依赖

第一步添加依赖略，参看上面**三种集成方式**的第一种。

### 2. 添加加密配置项

在 application.yml 配置文件中添加如下配置项：

```yaml
jasypt:
  encryptor:
    property:
      prefix: "DC@["
      suffix: "]"
```

配置中的 prefix 和 suffix 是**自定义**的密码串标识。其中还有一个必须的参数`jasypt.encryptor.password`，用于加密。但为了安全需要通过命令行方式传入，不能放入配置文件中。启动命令示例：

```yaml
java -Djasypt.encryptor.password=B2B36A2BECD039215094CCF3FA43AEE1 -jar xx.jar
```

其中`B2B36A2BECD039215094CCF3FA43AEE1`是自定义密码字符串，推荐 32 为 md5 码。

### 3. 敏感信息加密

编写加密测试类如下：

```java
package com.ahshumei.common.utils;

import com.ahshumei.BaseTest;
import org.jasypt.encryption.StringEncryptor;
import org.junit.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;

import javax.annotation.Resource;

/**
 * 对称加密算法测试类
 *
 * @author ethan
 * @datetime 2023/7/14 14:01
 */
public class StringEncryptorTest extends BaseTest {
    protected Logger logger = LoggerFactory.getLogger(getClass());

    @Resource
    StringEncryptor stringEncryptor;

    /**
     * 对称加密测试方法
     *
     * @author ethan
     * @datetime 2023/7/14 13:57
     */
    @Test
    public void encryptTest() {
        String usernameEnc = stringEncryptor.encrypt("empower");
        String passwordEnc = stringEncryptor.encrypt("teststr");

        System.out.println(usernameEnc);
        System.out.println(passwordEnc);
        logger.info("test username encrypt is {}ª", usernameEnc);
        logger.info("test password encrypt is {}", passwordEnc);

        logger.info("test username is {}", stringEncryptor.decrypt(usernameEnc));
        logger.info("test password is {}", stringEncryptor.decrypt(passwordEnc));
    }

}

```

### 4. 配置加密项

将需要加密的配置项明文通过上一步测试方式进行加密，然后将加密后密文配置进配置文件中，示例：

```yaml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driverClassName: com.mysql.cj.jdbc.Driver
    druid:
      # 主库数据源
      master:
        url: jdbc:mysql://10.10.1.7:3306/moon
        username: DC@[4Y2s7d6D8rVQ3NAS/oWT+08QZT3dLcqN95i8YDtujnY6eFYshKGHDdniy7R5vyymª]
        password: DC@[xImPkGcaY6qMK3vctVxkpKRg1KgxEG4hhMlgSQwiJhdueooHFGSUEccZFezeD2ig]
```

`DC@[]`占位符是上面配置文件中自定义配置的。

读取配置项测试类：

```java
package com.ahshumei.common.utils;

import com.ahshumei.BaseTest;
import org.junit.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;

/**
 * 对称加密算法测试类
 *
 * @author ethan
 * @datetime 2023/7/14 14:01
 */
public class StringEncryptorTest extends BaseTest {
    protected Logger logger = LoggerFactory.getLogger(getClass());

    @Value("${spring.datasource.druid.master.username}")
    private String username;
    @Value("${spring.datasource.druid.master.password}")
    private String password;

    /**
     * 测试读取加密配置项方法
     *
     * @author ethan
     * @datetime 2023/7/14 18:13
     */
    @Test
    public void decodeTest() {
        logger.info(username);
        logger.info(password);
    }

}
```

### 5. 总结

jasypt 开源库为 Spring 项目提供了一种加解密能力来保护项目中的敏感信息，通过以上 4 步简单的配置即可使用，我们不需要去了解复杂加解密机制即可使用。并且 jasypt 不仅能集成到 Spring 中，还能集成到 Apache、Hibernate、SpringSecurity 等框架中。
