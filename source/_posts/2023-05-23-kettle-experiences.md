---
title: kettle experiences
comments: true
categories:
  - tech
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

## SQL

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

- 类型转换

```sql
# 把字段 id 的类型转换为字符串 char
CAST(id as CHAR) AS CODE
```



## 设定变量

转换命名参数就是在转换内部定义的变量，作用范围是在转换内部。在转换的空白处双击左键，在转换属性中能看到“命名参数”。

引用：可以在表输入SQL语句中使用${变量名} 或者 %%变量名%%

# 作业执行

- 执行转换

```shell
pan.sh -file tr1.ktr
pan.sh -file tr1.ktr -param:year=2023 -param:month=5
```

- 执行作业

```shell
kitchen.sh -file jobname1.kjb
kitchen.sh -file jobname2.kjb -param:input=/kettle -param:output=/kettle
```

# 定时任务脚本

```shell
#!/bin/bash
# 环境变量
JAVA_HOME=/home/shumei/soft/jdk1.8.0_171
KETTLE_HOME="/home/shumei/soft/data-integration"
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=${JAVA_HOME}/bin::$PATH
# 公共变量
DATE_STR=`date +%Y%m%d`;
BASE_DIR='/home/shumei/kettle'
# 局部变量
v_script_dir='/kettle/1015dca/pro'
v_shell_name=dca1m
v_result="执行成功"
# 脚本名称
JOB01_NAME=tr1.ktr
JOB02_NAME=tr2.ktr

# X 企业名录入库
v_date_str=$(date +"%Y-%m-%d %H:%M:%S")
${KETTLE_HOME}/pan.sh -file ${BASE_DIR}${v_script_dir}/${v_job01_name} -level=Basic >> ${BASE_DIR}/logs/${v_shell_name}.${DATE_STR}
pan_return=$?
if [ $pan_return -eq 0 ]; then
    v_result="执行成功"
elif [ $pan_return -eq 1 ]; then
    v_result="执行失败"
else
    v_result="执行取消"
fi
echo $v_result
if [ $pan_return -ne 0 ]; then
    echo "Task X 企业名录入库-$v_result ${v_date_str}" | mail -s "Subject: Task Failed" ethan@ahshumei.com
fi
echo ${v_date_str}-X 企业名录入库 start
echo -e $(date +"%Y-%m-%d %H:%M:%S")-X 企业名录入库 end\n

sleep 2

```

