---
layout: default
title: Idea 常用配置
date: 2017-08-19 21:09:09
updated: 2019-11-21 18:35:10
categories:
  - 工具
  - idea
tags: 
  - idea
---
# 概述
总结的一些 idea 配置方法

## 隐藏 .idea 文件夹和 .iml 等文件（可选）

在 File -> Settings -> Editor -> File Types 下的 "Ignore files and folders" 中添加：
```
.idea;*.iml;
```
## 文件编码设置
File -> Settings -> Editor -> File Encodings 下设置：
```
Global Encoding: UTF-8
Projectt Encoding: UTF-8
Default encoding for properties files: UTF-8
```
勾选上 Transparent native-to-ascii conversion

## 生成 javadoc 配置
打开 Tools -> Gerenate JavaDoc
在 Other command line arguments 中输入：-encoding utf-8 -charset utf-8, 避免中文乱码

## 设置代码行宽度(Columns)
打开 File -> Other Settings -> Default Settings -> Editor -> Code Style
修改 Default Options -> Right Margin (Columns) 可以为所有类型的文件设置默认宽度，设置为 120。
另外可以在 Code Style 下特定类型文件（例如 Java、Jsp）的 Wrapping tab 下修改该类型文件的代码行宽度。

## 自定义 Live Template

Preferences -> Editor -> Live Templates 选择 Java, 添加自定义模板以及自己的快捷输入方法。

### 方法注释模板配置示例

- Abbreviation: mc
- Description: method comments
- Template Text内容：
```
/**
 * $notes$
 *
 $params$
 * @return $return$
 * @author LingJun
 * @datetime $DATE$ $TIME$
 */
```

params Default value:
```
 groovyScript("if(\"${_1}\".length() == 2) {return '';} else {def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList();for(i = 0; i < params.size(); i++) {if(i<(params.size()-1)){result+=' * @param ' + params[i] + '\\n'}else{result+=' * @param ' + params[i]}}; return result;}", methodParameters());
```

note: comments for this method


### 代码块注释示例


- Abbreviation: cc
- Description: class comments
- Template Text内容：
```
**
 * $notes$
 *
 * @author LingJun
 * @datetime $DATE$ $TIME$
 */
```

注意:
- 需要编辑 date，time 等变量的值。
- 日期的变量值推荐加参数如：date("yyyy/MM/dd")
- define 选择 java
- note: comments for this class

## 换行符
使用 Unix 格式，不要使用 Windows 格式。

## 缩进
禁止使用 tab 缩进，采用 4 个空格缩进。如果使用 tab 缩进，必须在 IDE 中 Use tab character。