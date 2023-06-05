---
layout: default
title: Mac 下 Svn 命令配置
date: 2016-09-21 21:09:09
categories:
  - 技术
tags: [svn]
---

## 研究原因

为了使用方便，通过配置或者创建了一些 shell 脚本，简化 svn 的常用操作

## 配置列表

### 1、配置全局忽略文件类型

编辑 svn 的 config 文件
```
vi ~/.subversion/config
```
如果”.subversion”目录不存在，运行”svn status”命令，虽然此命令会失败，但是会为你创建所需要的文件。

找到 global-ignores 一行，新增需要忽略的文件：

<pre><code>
global-ignores = *.o *.lo *.la *.al .libs *.so *.so.[0-9]* *.a *.pyc *.pyo *.iml target
   *.rej *~ #*# .#* .*.swp .DS_Store .idea *.iml *.lst logs
</code></pre>

### 2、添加命令脚本

vi svnadd.sh

```shell
svn status | grep '?' | awk -F' ' '{print $2}' | xargs svn add
```

### 3、删除命令脚本

vi svndel.sh

```shell
svn status | grep '!' | awk -F' ' '{print $2}' | xargs svn del --force
```

#### 创建命令别名

vi ~/.zshrc

```shell
alias svndel="sh ~/scripts/svndel.sh"
alias svnadd="sh ~/scripts/svnadd.sh"
```

执行命令使配置生效

```
source ~/.zshrc
```

配置生效后，进入svn仓库目录，即可通过命令 svnadd 或者 svndel 来添加或者删除文件；

最后通过命令 svn ci -m "comments" 来提交到服务器

*****

svnadd.sh 命令解释: 首先使用 svn status 命令列出所有改动，打 ? 号的是已经新增的文件但是还未标记加入库；再用 awk '{print $2}' 将抽离出来的文本结果处理，留下每一行的第二段文字，即文件名；最后使用 xargs 这个参数构造命令，将每一行的文本作为参数提供给 svn add，结果就是所有列出的文件都执行了一遍添加命令。
