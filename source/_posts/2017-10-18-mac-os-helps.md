---
layout: default
title: Mac 系统使用的一些经验总结
date: 2017-10-18 21:31:02
categories:
  - 工具
tags:
  - mac
---

1. 修改应用的默认语言

```shell
defaults write com.apple.iCal AppleLanguages '(zh-CN)'
defaults write com.apple.iCal AppleLanguages '(en-US)'
defaults write com.apple.Maps AppleLanguages '(zh-CN)'
```

1. DS_Store文件

.DS_Store 文件是 Mac OS 保存文件夹的自定义属性的隐藏文件，如文件的图标位置或背景色，相当于 Windows 系统中的 desktop.ini 文件。

1. 允许任何来源的软件安装

```shell
sudo spctl --master-disable
```

1. 重启程序

重启菜单栏

```shell
killall -KILL SystemUIServer
```

重启Dock

```shell
killall Dock
```

重启Finder

```shell
killall Finder
```

重启wifi

```shell

```
