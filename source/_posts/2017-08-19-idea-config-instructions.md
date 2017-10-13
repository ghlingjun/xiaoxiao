---
layout: default
title: Idea 常用配置
date: 2017-08-19 21:09:09
tags: idea
---
# 概述
总结的一些 idea 配置方法
<!-- more -->
1. 隐藏.idea文件夹和.iml等文件（可选）

> 在 File->Settings->Editor->File Types 下的 ”Ignore files and folders” 中添加：.idea;*.iml;

2. 文件编码设置
File->Settings->Editor->File Encodings 下设置：
```
Global Encoding: UTF-8
Projectt Encoding: UTF-8
Default encoding for properties files: UTF-8
勾选上 Transparent native-to-ascii conversion
```
3. 自动导入所有包
在 Intellij IDEA 一次只能导入单个包，没有像Eclipse快速导入包的快捷键Ctrl+Shift+O，但是Intellij IDEA下有个自动导入包的功能。

在File->Settings->Editor->General->Auto Import下进行配置:
```
Insert imports on paste: 复制代码的时候，对于导入的包是否需要进行询问的一个选项。
    ASK(有需要导入的包名时会弹提示框，问你要不要导入)
    NONE(有需要导入的包名时不会弹提示框，也不会自动导入)
    ALL(有需要导入的包名时会自动导入，不会弹提示框)
Show import popup: 当输入的类的声明没被导入时，会弹出一个选择的对话框
Optimize imports on fly: 自动优化包导入，移除不需要的包
Add unambiguous imports on the fly: 这个就是自动导入功能了，当你输入类名后声明就被自动导入了
Exclude from Import and Completion: 这个其实就是你自定义import,可以不用关注，一般来说是用不上的
```
4. 设置默认换行符

> File->Other Settings->Default Settings
> Editor->Code Style
> Line separator (for new files) 选择 Unix and OS X(\n)

5. 生成 javadoc 配置

> 在菜单栏选择Tools->Gerenate JavaDoc
> 在 Other command line arguments 中输入：-encoding utf-8 -charset utf-8, 避免中文乱码
