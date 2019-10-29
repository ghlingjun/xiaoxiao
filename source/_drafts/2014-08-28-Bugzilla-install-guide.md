---
layout: default
title: Bugzilla 安装配置
date: 2014-08-28 01:49:42
tags: bugzilla
---

1、安装 Perl
Perl 通常 Linux 系统会默认已经安装，请确认版本高于 5.8.1，如果系统没有安装 Perl, 请访问 http://www.perl.org. 最好的选择是安装最新的稳定版本。
2、安装配置 MySQL 数据库
sudo apt-get install mysql-server-5.6
$ mysql -u root -pyourpasswd
$ mysql>create database bugs;
$ mysql>grant select,insert,update,delete,index,alter,create,lock tables,create temporary tables,drop,references on bugs.* to bugs@localhost identified by 'bugsadmin';
$ mysql>FLUSH PRIVILEGES;
$ mysql>quit  
3、安装 Web 服务器
这里选择 Apache web server
访问http://httpd.apache.org/download.cgi下载安装包，并按照 http://httpd.apache.org/docs/2.4/install.html的安装说明进行编译安装。
解压源码包；
安装 Apache web server 需求如下：
APR and APR-Util ：从 Apache APR 下载 APR 和 APR-Util，然后分别解压到 ./httpd-NN/srclib/apr 和 ./httpd-NN/srclib/apr-util，
Perl-Compatible Regular Expressions Library (PCRE)：
从 http://www.pcre.org/ 下载源码，编译安装;
./configure –prefix=/usr/local/pcre
开始编译安装：
./configure –prefix=/usr/local/apache2 –with-pcre=/usr/local/pcre
make
sudo make install
4、配置 Bugzilla
下载 Bugzilla tar 包
然后解压，位置根据自己洗好，这里解压在如下：/usr/local，然后通过配置 Apache2/conf/httpd.conf 来告诉 Web 服务器 bugzilla 的位置：
Alias /bugzilla/ /usr/local/bugzilla/
然后让 web 服务的用户对这个目录有写入权限：
sudo chown –R www-data.www-data ./bugzilla
5、安装 Perl Modules
接下来的操作需要切换到 root 用户，直到安装完成后再切换回来。
检查你需要安装的模块，运行：
./checksetup.pl --check-modules

通过运行命令： perl install-module.pl –all 来试着自动安装所有需要的模块；之后再检查是否需要的模块都已经安装，如果还有缺失的，你可以手动自己下载安装；请查看Appendix C, Manual Installation of Perl Modules；

Unix下，可以执行 install-modules.pl 脚本来尝试安装：
perl install-module.pl <modulename>
mod_perl 的安装【未成功】

下载到源码包后解压，然后进入源码目录执行：

perl Makefile.PL MP_APXS=/usr/local/apache2/bin/apxs MP_APR_CONFIG=/usr/local/apache2/bin/apr-1-config

如果有遇到找不到某个库文件，例如：/usr/bin/ld: cannot find –lperl 时，可以使用命令：ll | grep libperl 去查看是否存在该库文件；

如果有对应库文件，但是名字带有版本号时可以创建一个软连接：ln -s  ./libperl.so.5.18   ./libperl.so

在 /usr/local/apache2/conf/httpd.conf 文件中添加如下配置：

LoadModule perl_module modules/mod_perl.so

执行命令 sudo ./checksetup.pl

检查安装需要的 perl 模块

命令执行成功会生成 localconfig 文件，修改文件中的如下内容：

$db_port = 0; 改为 $db_port = 3306; 
$index_html = 0;改为 $index_html = 1; 
$db_pass = ''; 改为 $db_pass = 'bugsadmin' 也就是上面数据库设置的密码。 
在命令行下再次运行 sudo ./checksetup.pl 将会生成和数据库有关的数据表， 
生成数据表后会要求填入主机的地址服务器地址，管理员名字和账号（该账号是一个email地址）以及管理员登陆的密码和确认密码。
