---
layout: default
title: Windows 下快捷启动应用程序的一个方法
date: 2013-11-21 06:06:42
categories:
  - 工具
tags: [方法]
---

这里介绍一个小窍门给 windows 用户——可以很快捷方便的启动自己常用的软件程序！

三步即可：
1. 在C盘目录下创建了一个名为 shoutcut 的文件夹；
2. 将 shoutcut 路径添加进环境变量的 path 值内（Win7 系统打开环境变量方法：控制面板-->高级系统设置-->高级 Tab -->环境变量）；
3. 将常用的应用程序的快捷方式复制到 shoutcut 目录，并为其重命名一个简单明了的名字，例如, Sublime Text 重命名为 sublime, Google Chrome 重命名为 chrome, 腾讯 QQ 重命名为 qq, 等等. 

完成上面三步后，即可快速打开应用程序，例如：按下快捷键 Win+R，输入 chrome，然后按下 Enter 键即可打开谷歌浏览器……

这个方法好吗？

<font style="color: gray; font-size: 10px;">不重要的内容:</font>
<span style="color: gray; font-size: 10px;">因为虚拟的系统比较卡，之前都是在 Windows 下编写修改代码，Ubuntu 系统只是提供 ROR（Ruby On Rails）环境用。前两天在 Ubuntu 下调试公司的 OA 系统时，在虚拟机中安装了 Sublime Text 用于编写代码。当创建 Sublime Text 的软链接时，突然想到其实在 Windows 下快捷方式也相当于软链接，所以启动程序也可以直接运行命令的方式启动。
我的电脑桌面一向就只有一个回收站图标，非常干净，这个是个人喜好，然后每次打开程序都必须从开始菜单打开，但是现在就像在Ubuntu下打开程序一样，非常爽快 :-)
</span>
