---
title: Ubuntu 下 Mantis 安装指南
date: 2014-12-03 13:42:00
tags: mantis ubuntu
---

## 第一步，安装 MySql
```
sudo apt-get install mysql-server-5.6
```
安装完数据库服务后，创建数据库 mantisdb
```
$ mysql -u root -pyourpasswd
$ mysql>create database mantisdb;
$ mysql>grant all privileges on mantisdb.* to mantis@localhost identified by 'mantisadmin';
$ mysql>FLUSH PRIVILEGES;
$ mysql>quit
```
## 第二步，安装 Web 服务器
访问 http://httpd.apache.org/download.cgi 下载 Apache web server 安装包，并按照 http://httpd.apache.org/docs/2.4/install.html 的安装说明进行编译安装。

1. 解压源码包
2. APR and APR-Util：从 Apache APR 下载 APR 和 APR-Util，然后分别解压到 ./httpd-NN/srclib/apr 和 ./httpd-NN/srclib/apr-util
3. Perl-Compatible Regular Expressions Library (PCRE)：从 http://www.pcre.org/ 下载源码，编译安装;
```
./configure –prefix=/usr/local/pcre
```
4. 开始编译安装：
```
./configure –prefix=/usr/local/apache2 –with-pcre=/usr/local/pcre
make
sudo make install
```
## 第三步，安装PHP
下载PHP源码，解压并进入目录执行：
./configure --with-mysql --with-apxs2=/usr/local/apache2/bin/apxs
可能会遇到如下错误：
error: xml2-config not found. Please check your libxml2 installation.
解决方法：sudo apt-get install libxml2-dev
make
sudo make install
编辑 /usr/local/apache2/conf/httpd.conf 添加如下配置：
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
ServerName localhost:80
Listen 127.0.0.1:80
Listen 192.168.3.127:80
然后重启一下 Apache：/usr/local/apache2/bin/apachectl -k restart
4、安装 mantisbt
解压 mantis 到 /usr/local/apache2/htdocs/mantis 目录；
修改权限： sudo chmod –R 755 mantis
cp config_inc.php.sample config_inc.php
然后进行配置：vi config_inc.php
$g_hostname      = 'localhost';
$g_db_username   = 'mantis';
$g_db_password   = 'mantisadmin';
$g_database_name = 'mantisdb';
$g_db_type       = 'mysql';
访问 http://ip/mantis/admin/install.php 进行安装：
SYSTEM WARNING: 'date_default_timezone_get(): It is not safe to rely on the system's timezone settings. You are *required* to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier. We selected the timezone 'UTC' for now, but please set date.timezone to select your timezone.' in '/usr/local/apache2/htdocs/mantis/core.php' line 274
如果报以上错误，编辑core.php文件，在文件开始处加一行代码：date_default_timezone_set('Asia/Shanghai'）;
直接点击页面最下方的“Install/Upgrade Database”按钮。
点击最下方的 continue，进入登录界面，输入用户名密码，默认为：
administrator root
5、汉化
sudo vi /usr/local/apache2/htdocs/mantis/config_defaults_inc.php
找到代码：$g_default_language                = 'english';
修改 english 为 chinese_simplified，保存即可。
6、配置 email
修改 /usr/local/apache2/htdocs/mantis/config_inc.php 中 smtp 服务相关的设置:

# --- Email Configuration ---
$g_phpMailer_method             = PHPMAILER_METHOD_SMTP; # or PHPMAILER_METHOD_MAIL, PHPMAILER_METHOD_SENDMAIL
$g_smtp_host                    = 'smtp.exmail.sina.com'; # used with PHPMAILER_METHOD_SMTP
$g_smtp_username                = 'noreplay@primecocn.com'; # used with PHPMAILER_METHOD_SMTP
$g_smtp_password                = 'nr654321'; # used with PHPMAILER_METHOD_SMTP
#$g_smtp_connection_mode                = 'ssl';
#$g_smtp_port                   = 465;

$g_administrator_email  = 'zhanglingjun@primecocn.com';$g_webmaster_email      = 'zhanglingjun@primecocn.com';$g_from_email           = 'noreplay@primecocn.com';     # the "From: " field in emails
$g_return_path_email    = 'noreplay@primecocn.com'; # the return address for bounced mail
$g_from_name                    = 'Mantis Bug Tracker';


测试 phpMailer 代码：（将代码保存为 test.html, 然后在命令行执行 php test.html 可测试是否能发送成功）

<?phprequire 'class.phpmailer.php';
$mail = new PHPMailer;
$mail->IsSMTP();                       // Set mailer to use SMTP
$mail->Host = 'smtp.exmail.sina.com';  // Specify main and backup server
$mail->SMTPAuth = true;                               // Enable SMTP authentication
$mail->Username = 'noreplay@primecocn.com';                            // SMTP username
$mail->Password = 'nr654321';                           // SMTP password

$mail->From = 'noreplay@primecocn.com';$mail->FromName = 'noreplay';$mail->AddAddress('chengliu@primecocn.com', 'chengliu');  // Add a recipient
$mail->AddAddress('guanhongqi@primecocn.com');               // Name is optional
//$mail->AddReplyTo('noreplay@primecocn.com', 'Information');
$mail->AddCC('zhaozhizhong@primecocn.com');$mail->AddBCC('liuchuanju@primecocn.com');$mail->AddBCC('zhanglingjun@primecocn.com');
$mail->WordWrap = 50;                                 // Set word wrap to 50 characters
//$mail->AddAttachment('/var/tmp/file.tar.gz');         // Add attachments
//$mail->AddAttachment('/tmp/image.jpg', 'new.jpg');    // Optional name
$mail->IsHTML(true);                                  // Set email format to HTML

$mail->Subject = 'Here is the subject';$mail->Body    = 'This is the HTML message body <b>in bold!</b>';$mail->AltBody = 'This is the body in plain text for non-HTML mail clients';
if(!$mail->Send()) {
   echo 'Message could not be sent.';
   echo 'Mailer Error: ' . $mail->ErrorInfo;
   exit;
}
echo 'Message has been sent.';

7、让管理员控制用户密码

修改Mantisbt PHP程序，增加一个密码修改框，让管理员可以直接修改用户密码。

sudo vi mantis/manage_user_edit_page.php
找到<!-- Email -->位置，在其下面加入以下代码：
<!-- Password -->
<tr <?php echo helper_alternate_class( 1 ) ?>>
    <td class="category" width="30%">
        <?php echo "Password (change only)" ?>:
    </td>
    <td width="70%">
        <input type="text" size="16" maxlength="100" name="password" value="" />
    </td>
</tr>

sudo vi mantis/manage_user_update.php

找到 $f_user_id = gpc_get_int( 'user_id' ); 在其下面加入以下代码：

$f_pass = gpc_get_string('password');

sudo vi mantis/manage_user_update.php

找到该行: $result = db_query( $query );
有的版本是：$result = db_query_bound( $query, $query_params );
在该行下面加入代码：
//Reset the password if specified.
 if ($f_pass) user_set_password($f_user_id, $f_pass);
这样就增加了Password (change only): 一列，以供管理员修改用户的密码（但是前提是管理员增加用户时不要勾选保护一项，否则管理员无法修改该用户密码）

