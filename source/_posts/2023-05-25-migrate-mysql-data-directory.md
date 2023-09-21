---
title: 迁移 mysql 数据目录
comments: true
categories:
  - tech
tags: [mysql]
date: 2023-05-25 08:59:25
updated: 2023-05-25 08:59:25
---

在 CentOS 中迁移 MySQL 的数据目录 /var/lib/mysql 到其他磁盘可以按以下步骤进行:

1. 停止MySQL服务

```shell
sudo service mysqld stop
```

2. 新建目标数据目录，假设新的数据目录为 /home/mysql/data，创建该目录：

```shell
sudo mkdir /home/mysql/data
```

3. 将原数据目录中的内容复制到新目录

```shell
sudo cp -r /var/lib/mysql /home/mysql/data
```

4. 修改新数据目录权限

```shell
sudo chown -R mysql:mysql /home/mysql/data/mysql
sudo chmod -R 755 /home/mysql/data/mysql
```

5. 修改 MySQL 配置编辑MySQL配置文件 /etc/my.cnf，在 [mysqld] 部分添加：

```shell
log-bin = /home/mysql/data/mysql/hfgc-5-bin
log_bin_index = /home/mysql/data/mysql/hfgc-5-bin.index
datadir=/home/mysql/data/mysql
socket=/home/mysql/data/mysql/mysql.sock
```

将 datadir、log-bin 等相关项的值修改为新的数据目录路径。

6. 重新启动MySQL服务

```shell
sudo service mysqld start
```

 MySQL会自动使用新的 datadir 路径启动。

7. 检查 MySQL 是否正常运行进入 MySQL 命令行,检查数据库、表等是否正常：

```sql
show databases;
use test; 
show tables;
```

确认无异常后说明迁移成功。

8. 删除原数据目录（可选）确认迁移成功后可以删除原数据目录 /var/lib/mysql。

以上步骤通过修改配置、复制数据并修改权限的方式完成 MySQL 数据目录的迁移。

