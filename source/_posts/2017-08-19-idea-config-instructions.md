---
layout: default
title: Idea 常用配置
date: 2017-08-19 21:09:09
tags: idea
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
## 自动导入所有包
在 Intellij IDEA 一次只能导入单个包，没有像Eclipse快速导入包的快捷键 Ctrl+Shift+O，但是 Intellij IDEA 下有个自动导入包的功能。

在 File -> Settings -> Editor -> General -> Auto Import 下进行配置:

Insert imports on paste: 复制代码的时候，对于导入的包是否需要进行询问的一个选项。
    ASK(有需要导入的包名时会弹提示框，问你要不要导入)
    NONE(有需要导入的包名时不会弹提示框，也不会自动导入)
    ALL(有需要导入的包名时会自动导入，不会弹提示框)
Show import popup: 当输入的类的声明没被导入时，会弹出一个选择的对话框
Optimize imports on fly: 自动优化包导入，移除不需要的包
Add unambiguous imports on the fly: 这个就是自动导入功能了，当你输入类名后声明就被自动导入了
Exclude from Import and Completion: 这个其实就是你自定义import,可以不用关注，一般来说是用不上的

## 设置默认换行符
打开 File -> Other Settings -> Default Settings -> Editor -> Code Style
设置 Line separator (for new files) 为 Unix and OS X(\n)

## 生成 javadoc 配置
打开 Tools -> Gerenate JavaDoc
在 Other command line arguments 中输入：-encoding utf-8 -charset utf-8, 避免中文乱码

## 设置代码行宽度(Columns)
打开 File -> Other Settings -> Default Settings -> Editor -> Code Style
修改 Default Options -> Right Margin (Columns) 可以为所有类型的文件设置默认宽度。
另外可以在 Code Style 下特定类型文件（例如 Java、Jsp）的 Wrapping tab 下修改该类型文件的代码行宽度。

## 自定义 Live Template
Preferences -> Editor -> Live Templates
选择 UserDefined, 添加自定义模板以及自己的快捷输入方法
