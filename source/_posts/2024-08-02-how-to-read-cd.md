---
title: Ubuntu 系统如何读取 CD？
comments: true
categories:
  - 运维
tags:
  - cd
date: 2024-08-02 16:59:18
updated: 2024-08-02 16:59:18
---

在 Ubuntu 中，你可以使用命令行工具来读取光盘中的数据。以下是一个简单的步骤说明和示例代码：
- 插入光盘到光驱。
- 打开终端。
- 使用 lsblk 命令来确定光盘的设备路径（例如：/dev/cdrom 或 /dev/sr0）。
- 如果需要，你可以挂载光盘到文件系统中，以便能够访问其数据。

使用文件浏览器或命令行工具来读取光盘上的文件。

以下是一个示例命令序列，用于挂载光盘并复制其内容到当前目录：
```shell
# 确定光盘设备，例如 /dev/cdrom 或 /dev/sr0
lsblk

# 创建一个挂载点，例如在 /mnt/cdrom
sudo mkdir /mnt/cdrom

# 挂载光盘到这个挂载点
sudo mount /dev/cdrom /mnt/cdrom

# 复制光盘内容到当前目录
cp -r /mnt/cdrom/. ./

# 卸载光盘
sudo umount /mnt/cdrom

# 删除挂载点（如果不再需要）
sudo rmdir /mnt/cdrom
```
