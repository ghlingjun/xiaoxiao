---
title: linux 常用命令
date: 2019-10-29 17:55:39
updated: 2019-10-30 17:55:39
comments: true
categories:
  - 运维
tags: [linux]
---

## 命令介绍
### du
当前目录只进入第一级目录
```
du -h --max-depth=1
du -h -d1
```

### free 查看内存情况
```
free -m
```

### top
```
top
```

### vmstat
```
vmstat
```

### tree
```
tree -d -L 3
```

### 挂在硬盘
```
blkid -o device
mount /dev/xvde1 /home
```

### 重启定时任务
```
sudo /etc/init.d/crond restart
```

### 修改密码
```
id # 查看用户信息
passwd # 修改用户密码
```

### 修改 ssh 服务端口号
查看端口是否被占用：
```
sudo netstat -anp | grep 10007
```
添加配置：
```
sudo vi /etc/ssh/sshd_config
```
修改 Port 值：
Port 10007

重启 ssh 服务：
```
sudo service sshd restart
```
### curl
```
curl -Iv www.baidu.com
curl -Iv http://172.17.0.15
curl 129.211.135.212:80
```

### telnet
```
telnet 172.17.0.15 80
```

### 查看系统版本
```
lsb_release -a
```

### 查看文件状态
```
stat /etc/my.cnf
```

### 查看指定时间之前的登陆记录
```
last -t 20220410000000
```

### 环境变量配置
```shell
alias sshdb="ssh ethan@192.168.1.79"

export JAVA_HOME=/home/shumei/software/jdk1.8.0_25
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

## 日期时间函数

```shell
# 取日期 yyyy-mm-dd
date +%F
# 取上个月月份（两位数字符串）
date -d "2023-06-06 -1 month" +%m
# 取上个月月份（数字）
month=`date -d "2023-06-06 -1 month" +%m`
$((month))
# 取上一年年份
date -d "2023-06-06 -1 year" +%Y

v_date=`date +%F`
# 取上个月字符串
`date -d "$v_date -1 month" +%Y%m`

v_date_now=`date +"%F %T"`
# 取前 15 分钟的时间
v_date_time=`date -d "-15 minute $v_date_now" +"%F %T"`
```

## 其他

```
80,8080,443,8443 需要备案
```

