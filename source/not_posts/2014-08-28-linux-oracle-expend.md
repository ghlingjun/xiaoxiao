---
layout: default
title: Linux 下 Oracle 扩容
date: 2014-08-28 01:49:42
tags: oracle
---

切换至Oracle用户：
```
$ su – oracle
```
登录sqlplus：
```
$ sqlplus / as sysdba
```
启动数据库
```
SQL> start
```
启动监听
```
$ lsnrctl start
```
查看数据库中所有的表空间：
```
SQL> select * from v$tablespace;
```
查看表空间有那些文件组成：
```
SQL> desc dba_data_files;
SQL> select file_name,tablespace_name from dba_data_files;
```
查看各表空间分配情况：
```
SQL> select file_name, tablespace_name, (bytes)/1024/1024 from dba_data_files;
```
查看查看各表空间空闲情况：
```
SQL> select tablespace_name, sum(bytes)/1024/1024 from dba_free_space group by tablespace_name;
SQL> SELECT t1 tablespace, z total_space, z - s used_space, s unused_space, ROUND((z - s) / z * 100, 2) "used%total"
From (Select tablespace_name t1, Sum(bytes) s
   From DBA_FREE_SPACE
   Group by tablespace_name), 
   (Select tablespace_name t2, Sum(bytes) z From DBA_DATA_FILES Group by tablespace_name)
Where t1 = t2;
```
更改表空间大小：
```
SQL> alter database datafile '/home/app/oracle/product/10.0.20/db_1/oradata/STDM/TBS_DATA04.dbf' resize 8192m;
SQL> alter database datafile '/home/app/oracle/product/10.0.20/db_1/oradata/STDM/TBS_DATA05.dbf' resize 7168m;
```
查看表空间是否自动增长:
```
SQL> SELECT FILE_NAME, TABLESPACE_NAME, AUTOEXTENSIBLE FROM dba_data_files;
```
设置表空间自动增长：
```
SQL> ALTER DATABASE DATAFILE '/home/app/oracle/product/10.0.20/db_1/oradata/STDM/TBS_DATA05.dbf' AUTOEXTEND ON; //打开自动增长 
SQL> ALTER DATABASE DATAFILE '/home/app/oracle/product/10.0.20/db_1/oradata/STDM/TBS_DATA05.dbf' AUTOEXTEND ON NEXT 200M ; // 每次自动增长200m 
SQL> ALTER DATABASE DATAFILE '/home/app/oracle/product/10.0.20/db_1/oradata/STDM/TBS_DATA05.dbf' AUTOEXTEND ON NEXT 200M MAXSIZE 1024M; // 每次自动增长200m，数据表最大不超过1G
```
