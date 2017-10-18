---
layout: default
title: SVN 配置
date: 2016-10-15 21:09:09
tags: svn
---

# 概述
总结的一些不常用的 svn 操作命令

## svn 服务启动命令
```
sudo svnserve -d -r /data/svn
sudo killall svnserve
```
## ignore 命令
忽略文件夹
<pre><code>
svn prospect svn:ignore ‘schema’ .
</code></pre>
忽略已存在的文件
<pre><code>
svn export config.properties config.properties-tmp
svn rm config.properties
svn ci -m ‘remove config.properties’
mv config.properties-tmp config.properties
svn propset svn:ignore ‘config.properties’ .
svn ci - ‘ignore config.properties’
</code></pre>
### 忽略文件夹中某几个文件
> 在文件夹中新增 .svnignore 文件，在 .svnignore 中添加要忽略的文件，例如：
```
.list
.iml
.idea
</code></pre>
```
> 然后：
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
