---
layout: default
title: Github 入门
date: 2013-01-28 07:34:42
categories:
  - 工具
  - git
tags:
  - git
---

## 创建一个Repo

参考：<https://help.github.com/articles/create-a-repo>

### 初始化一个Repo

```shell
$ mkdir ~/Hello-World
# Creates a directory for your project called "Hello-World" in your user directory

$ cd ~/Hello-World
# Changes the current working directory to your newly created directory

$ git init
# Sets up the necessary Git files

# Initialized empty Git repository in /Users/you/Hello-World/.git/
```

### 为你的项目创建 README 文件

```shell
$ touch README
# Creates a file called "README" in your Hello-World directory

然后编辑这个文件，输入“你好”。保存并关闭它。
```

### 提交 README 文件

```shell
现在提交你的README文件。提交本质上是一个您的项目中的所有文件在一个特定时间点的快照。在提示符下，输入下面的代码：
$ git add README
# Stages your README file, adding it to the list of files to be committed

$ git commit -m 'first commit'
# Commits your files, adding the message "first commit"
```

### 推送提交的文件到服务器

```shell
到现在你所做的操作仍是在本地仓库中，新增以及修改的内容都还没有添加到github上。为本地仓库设置一个远程仓库，把本地仓库和你的github账户连接起来，然后把文件推送到github上。
$ git remote add origin https://github.com/username/Hello-World.git
# Creates a remote named "origin" pointing at your GitHub repo

$ git push origin master
# Sends your commits in the "master" branch to GitHub
```

现在看你的github上的仓库，你会发现README文件已经添加在里面了。

## 加入一个开源项目

参考：<https://help.github.com/articles/fork-a-repo>

也许某天你发现一个有趣的项目，自己也想加入项目开发组为项目做点贡献；或者你想在别人的项目的基础上开发自己的东西，这个过程就是 forking。这里介绍如何 fork 一个项目: Fork the “Spoon-Knife” Repo.

点击 github 中的 fork 按钮即可 Fork 项目。

Forked项目后，你需要clone项目到本地才可以对项目修改，执行下面命令：

```shell
$ git clone https://github.com/username/Spoon-Knife.git
# Clones your fork of the repo into the current directory in terminal
```

这样我们复制了代码库到本地，不过这个默认指向我们 github 中的项目，我们叫它 origin, 并不是最初我们 Fork 的项目。为了跟踪源库的变动，我们需要和最初的仓库（叫它为 upstream ）联系起来：

```shell
$ cd Spoon-Knife
# Changes the active directory in the prompt to the newly cloned "Spoon-Knife" directory

$ git remote add upstream https://github.com/octocat/Spoon-Knife.git
# Assigns the original repo to a remote called "upstream"

$ git fetch upstream
# Pulls in changes not present in your local repository, without modifying your files
```

That's it. 我们准备好了。

后面我们可以做些更有趣的事

### Push commits

如果你提交了一些修改到你的本地代码库，现在想把这些推送到 github ，执行下面命令：

```shell
$ git push origin master
# Pushes commits to your remote repo stored on GitHub
```

### Pull in upstream changes

如果原始代码库有更新，你可以执行下面命令，将那些更新也同步到你的代码库：

```shell
$ git fetch upstream
# Fetches any new changes from the original repo

$ git merge upstream/master
# Merges any changes fetched into your working files
```

### Create branches

有时你想给项目添加个新的特性或者测试一个想法，你肯定不愿意直接在项目上做，以免对项目造成未知的影响，这时你可以创建一个项目的分支。在 git 中，分支并不是把代码库复制一边，而是 a sort of bookmark that references the last commit made in the branch. 所以分支是很小的并且很容易处理。

项目分支是非常方便的，特别是当你和许多人一起为一个项目工作时。创建分支命令：

```shell
$ git branch mybranch
# Creates a new branch called "mybranch"

$ git checkout mybranch
# Makes "mybranch" the active branch

或者：
$ git checkout -b mybranch
# Creates a new branch called "mybranch" and makes it the active branch

切换分支，使用命令 git checkout.
$ git checkout master
# Makes "master" the active branch

$ git checkout mybranch
# Makes "mybranch" the active branch

当你在分支上完成工作后，想将它和主分支合并时，使用命令： merge
$ git checkout master
# Makes "master" the active branch

$ git merge mybranch
# Merges the commits from "mybranch" into "master"

$ git branch -d mybranch
# Deletes the "mybranch" branch
```

### Pull requests

如果你想将你的代码贡献给最初的项目，可以发送 pull request 给项目创建者。

Celebrate!
