---
title: Mac 系统使用的一些经验总结
date: 2017-10-18 21:31:02
tags: mac
---
1. 修改应用的默认语言
```
defaults write com.apple.iCal AppleLanguages '(zh-CN)'
defaults write com.apple.iCal AppleLanguages '(en-US)'
defaults write com.apple.Maps AppleLanguages '(zh-CN)'
```

2. DS_Store文件
.DS_Store 文件是 Mac OS 保存文件夹的自定义属性的隐藏文件，如文件的图标位置或背景色，相当于 Windows 系统中的 desktop.ini 文件。

3. 允许任何来源的软件安装
```
sudo spctl --master-disable
```

4. 重启程序
重启菜单栏
```
killall -KILL SystemUIServer
```
重启Dock
```
killall Dock
```
重启Finder
```
killall Finder
```
重启wifi
```

```