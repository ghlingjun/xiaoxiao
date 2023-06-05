---
layout: default
title: SVN 使用
date: 2016-10-15 21:09:09
categories:
  - 技术
tags: [svn]
---

# 概述
总结的一些不常用的 svn 操作命令

## svn 服务启动命令
```
sudo svnserve -d -r /data/svn
sudo killall svnserve
```
## ignore 命令
**忽略文件夹**
```
svn propset svn:ignore "
>schema
>.back
>" .
```
注意:写值的时候不要一下将两个引号写完，否则回车会直接执行命令。
svn:ignore的值每行一个

**忽略已存在的文件**
```
svn export config.properties config.properties-tmp
svn rm config.properties
svn ci -m 'remove config.properties'
mv config.properties-tmp config.properties
svn propset svn:ignore 'config.properties' .
svn ci - 'ignore config.properties'
```
**忽略文件夹中某几个文件**
在文件夹中新增 .svnignore 文件，在 .svnignore 中添加要忽略的文件，例如：
```
.list
.iml
.idea
```
然后执行如下命令：
```
svn prospect svn:ignore -F .svnignore .
```
## add a tag
```
!/bin/bash
export VERSION=v1.2.0
svn cp http://code.taobao.org/svn/nakri/trunk http://code.taobao.org/svn/nakri/tags/$VERSION
```
## 重定位location
```
svn relocate url
```
## E155010: Commit failed
"xxx" is scheduled for addition, but is missing 遇到此错误提示时执行下面命令即可：
```
svn rm xxx
```
这个错误原因是文件被 add 进本地仓库后，又被删除了，提交到服务器时 svn 找不到对应的文件。
