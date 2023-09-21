---
title: mac 系统常用工具安装
comments: true
categories:
  - tech
tags: [mac,brew]
date: 2020-08-22 20:57:31
updated: 2020-08-22 20:57:31
---
# Homebrew
简单记录下 Homebrew 安装。

Homebrew 可以在 macOS 中方便的安装和管理各种系统不自带的开发包。但令人苦恼的是很多时候它的下载和更新速度太慢，推荐国内自动安装 Homebrew 的脚本。

项目名称：HomebrewCN
项目作者：CunKai
项目地址：https://gitee.com/cunkai/HomebrewCN
脚本内容
```
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```
只需要把这段脚本内容复制到「终端」中执行，按提示安装。

# autojump
brew install autojump

# docker
阿里云镜像获取地址：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

