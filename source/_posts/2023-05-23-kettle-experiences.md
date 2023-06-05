---
title: kettle experiences
comments: true
categories:
  - 技术
tags: [kettle]
date: 2023-05-23 11:56:35
updated: 2023-05-23 11:56:35
---

# kettle 部署

1. 解压 pdi-ce 压缩包
2. 放入 mysql 驱动至 lib 文件夹下
3. 配置环境变量 .profile

  ```shell
export KETTLE=/home/shumei/soft/data-integration
export PATH=${KETTLE}:$PATH
  ```

# 经验

## 类型转换

- 日期转换

```sql
date_format(a.start_date, '%Y/%m/%d 00:00:00.000') as start_date
date_format(create_time, '%Y/%m/%d %T.%f') as data_create_time
date_format(a.payedDate, '%Y/%m') as payedDate
date_format(DATE_ADD(now(), INTERVAL -1 MONTH), '%Y%m');
```

- 日期计算

```sql
a.update_time >= DATE_ADD(now(), INTERVAL -15 MINUTE)
a.update_time >= DATE_ADD(now(), INTERVAL -3 DAY)
a.update_time >= DATE_ADD(now(), INTERVAL -1 MONTH)
a.update_time >= DATE_ADD(now(), INTERVAL -1 YEAR)
```

## 设定变量



# 作业执行

- 执行转换

```shell
pan.sh -file tr1.ktr
pan.sh -file tr1.ktr -param:payedDate=202304
```

- 执行作业

```shell
kitchen.sh -file jobname1.kjb
kitchen.sh -file jobname2.kjb -param:input=/kettle -param:output=/kettle
```

# 定时任务脚本

```shell
#!/bin/bash
JAVA_HOME=/home/shumei/software/jdk1.8.0_171
KETTLE_HOME="/home/shumei/software/data-integration"
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin::$PATH

DATE_STR=`date +%Y%m%d`;
BASE_DIR='/home/shumei/kettle'

SCRIPT_DIR='/kettle/1015dca/pro'
SHELL_NAME=dca

JOB01_NAME=tr1.ktr
JOB02_NAME=tr2.ktr

# X 企业名录入共享库
echo ---------------------------
echo $(date +"%Y-%m-%d %H:%M:%S")-start
${KETTLE_HOME}/pan.sh -file ${BASE_DIR}${SCRIPT_DIR}/${JOB01_NAME} -level=Basic >> ${BASE_DIR}/logs/${SHELL_NAME}.${DATE_STR}
pan_return=$?   # 捕捉 pan 命令返回值
if [ $pan_return -eq 0 ]; then
    echo "执行成功"
elif [ $pan_return -eq 1 ]; then
    echo "执行失败"
else
    echo "执行取消"
fi
echo $(date +"%Y-%m-%d %H:%M:%S")-stop

sleep 2

# Y 企业名录入共享库
echo ---------------------------
echo $(date +"%Y-%m-%d %H:%M:%S")-start
${KETTLE_HOME}/pan.sh -file ${BASE_DIR}${SCRIPT_DIR}/${JOB02_NAME} -level=Basic >> ${BASE_DIR}/logs/${SHELL_NAME}.${DATE_STR}
pan_return=$?
if [ $pan_return -eq 0 ]; then
    echo "执行成功"
elif [ $pan_return -eq 1 ]; then
    echo "执行失败"
else
    echo "执行取消"
fi
echo $(date +"%Y-%m-%d %H:%M:%S")-stop

# m h  dom mon dow   command
# 每月 1 日同步企业名录至共享库
# 0 1 1 * * sh /home/shumei/kettle/kettle/shell/dca.sh >> /home/shumei/kettle/logs/crontab.log
```

