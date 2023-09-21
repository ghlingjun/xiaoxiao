---
title: Maven 插件 codemother 使用说明
date: 2018-05-09 12:29:57
categories:
  - tech
tags: [maven,codemother]
---

项目地址:[https://gitee.com/osethan/codemother-maven-plugin](https://gitee.com/osethan/codemother-maven-plugin)

### What is this repository for? ###

* 通过简单配置，生成大量的基础代码，达到快速开发的目的

### TODO ###



### How do I get set up? ###

* 创建模板文件夹

    将本项目中codemother目录复制到自己的项目的根目录下。
    
    template下的模板可根据实际需要进行修改、新增和删除，模版语言是 freemarker，极易上手。

* 创建 schema 目录

    schema目录用于存放业务表对应的xml配置文件（生成方法附在文档末），codemother基于此配置文件以及模板生成代码。
    
    在项目或模块中的resources目录下创建文件夹com/ethanxx/codemother，其下包含 dbschema文件夹以及global.properties文件，
    
    dbschema文件夹用于存放业务表对应的xml配置文件,global.properties配置信息实例如下：
    ```
    # 默认即可，无需更改
    baseOutputPath=.
    outputPathJava=src/main/java
    outputPathJavaResource=src/main/resources
    outputWebPath=src/main/webapp
    outputPagePath=src/main/webapp/WEB-INF/page/
    javaOutputPath=src/main/java
    resourceOutputPath=src/main/resources
    
    # 需要根据模块的包名修改
    projectId=com.primeco.log
    packagePath=com/primeco/log
    modelPackage=com.primeco.log.model
    mapperPackage=com.primeco.log.mapper
    servicePackage=com.primeco.log.service
    controllerPackage=com.primeco.log.ctl
    controllerBasePackage=com.primeco.log.ctl.base
    ```
* 配置maven依赖
  
    在项目或者模块pom.xml中配置插件依赖：
    ```
    <!--codemother 插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>com.ethanxx</groupId>
                <artifactId>codemother-maven-plugin</artifactId>
                <version>1.1.3</version>
            </plugin>
        </plugins>
    </build>
    ```
    
* How to run tests

    进入要生成代码的项目或者模块目录下，执行命令：`mvn codemother:code-generate`
    
    倘若你的项目是单模块，执行命令：`mvn codemother:code-generate -DtemplatePath=./codemother/template`, 这是因为`templatePath`默认值为`../codemother/template`, 用于多模块项目。
    
    templatePath的值实际依赖于src目录与template的项目路径，可以自定义temlate目录位置，可根据实际需要传参数。
    
    多模块项目目录结构示例：
    ```
    .
    ├── codemother
    │   ├── codemother.properties
    │   ├── dbschema
    │   └── template
    ├── README.md
    ├── pom.xml
    ├── model1
    │   ├── pom.xml
    │   ├── src
    │   │   └── main
    │   │       └── java
    └── model2
        ├── pom.xml
        ├── src
        │   ├── main
        │   │   ├── java
        │   │   └── resources
        │   └── test
                └── java
    ```
    假设要在 model1 模块中生成代码，则执行命令：`mvn codemother:code-generate`
    
    单模块项目目录结构示例：
    ```
    .
    ├── codemother
    │   ├── codemother.properties
    │   ├── dbschema
    │   └── template
    ├── pom.xml
    └── src
        ├── main
        │   ├── java
        │   ├── resources
        │   └── webapp
        └── test
            ├── java
            └── resources
    ```
    此项目生成代码则需要执行命令：
    `mvn codemother:code-generate -DtemplatePath=./codemother/template`
    
### Schema文件生成方法
* 通过SchemaGenerator工具类生成

    工具类通过解析配置文件codemother.properties，连接数据库，根据配置中制定的数据表名，生成对应的schema文件

* 配置schema文件

    生成的schema文件可以直接使用，但可以根据需要做高级配置，具体配置参数说明如下：
    ```
        <schema tableName="ACHIEVEMENT_ALLOCATION" modelName="AchievementAllocation" paged="true" noController="true">
            <key name="id" type="integer" length="10" generator="native"></key>
        
            <column name="account_number" type="varchar" length="64"></column>
        
            <default-condition>
                <match column="user_id" param="userId"/>
                <match column="status_cd" param="statusCd"/>
                <like column="username"/>
                <match-value column="record_flag" value="1"/>
                <between column="last_login_time" javaType="java.sql.Timestamp" low="low" high="high"/>
                <in column="id"/>
            </default-condition>
        
            <default-order>
                <order-by column="username" asc="false"/>
            </default-order>
        
            <alias name="user"/>
        </schema>
    ```
    * 标签 key 描述了表的主键，包含属性 name,type,length 等; 当主键为序列时，添加 generator 属性，值为 native;
    * 标签 match 表示查询时精确匹配；
    * 标签 not-match 表示不匹配查询；
    * 标签 match-value 表示查询时按特定的值进行查询；
    * 标签 like 表示查询时模糊匹配；
    * 标签 like-left 表示左模糊匹配；
    * 标签 like-right 表示右模糊匹配；
    * 标签 between 表示范围查询，low 和 high 分别表示查询范围的上下限；边界值是否包含取决于数据库类型（例如db2是左闭右开），如果边界值只传一个 low 或者 high，则按大于等于 low 或者小于等于 high 查询；
    * 标签 in 表示包含查询（查询效率低，慎用），其参数值一个数组，如："value1,value2,value3".split(",");
    * 标签 not-in 表示不包含查询（查询效率低，慎用），其参数值一个数组，如："value1,value2,value3".split(",");
    * 标签 order-by 表示查询结果按该字段进行排序；
    
    * 属性 column 指的表示 Table 的字段名；
    * 属性 javaType 表示数据库字段类型对应在 Java 中的数据类型；缺省该属性则默认为 String；
    * 属性 searchType 表示该字段在 Controller 接收页面传来的值的类型；缺省该属性则默认为 String；
    * 属性 param 表示该字段在 Java 代码中的命名，以驼峰形式命名；缺省该属性则默认为 column 的值；
    * 属性 asc 的值决定了查询结果的排序方式，false 表示按降序排列，缺省该属性或者设置为 true 表示按升序排列结果；
