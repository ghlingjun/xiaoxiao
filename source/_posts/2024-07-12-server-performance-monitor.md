---
title: 服务器性能监控及钉钉通知方案
comments: true
categories:
  - 运维
tags: 
  - message
date: 2024-07-12 18:57:17
updated: 2024-07-12 18:57:17
---

本方案实现思路是通过 shell 脚本查询服务器相关性能指标，并将性能指标记录下来，如果发现指标超出预设的阈值，则发送钉钉通知给相关人员。具体实现思路如下：

# 创建脚本目录
使用如下命令创建所需目录：
`mkdir -p /home/ethan/shell/log/monitor`
使用`-p`选项，这样如果上层目录不存在，它会自动创建它们。其中用户目录`ethan`根据用户名修改。

# 创建监控脚本
在`/home/ethan/shell`目录下创建 shell 脚本`server-monitor.sh`，内容参考附件【监控脚本】。
- 修改脚本中服务器名称、磁盘目录等变量值；
- 修改脚本执行权限：`chmod 755 server-monitor.sh`
- 测试脚本：`sh server-monitor.sh`

# 创建定时任务
执行`crontab -e`，添加定时任务，示例如下：
```shell
# 服务器性能监控，每天 10:24 执行一次
24 10 * * * sh /home/ethan/shell/server-monitor.sh >> /home/ethan/shell/crontab.log
```

# 附件
## 监控脚本
1. 脚本中常量`SERVER_NAME、DINGDING_ACCESS_TOKEN、DISK_PATH、LOG_PATH`值根据实际进行修改。
2. 发送邮件通知可选。删掉函数 send_mail及两处调用代码即可禁用。启用发送邮件功能需要安装 mail组件，安装手册可参考文章 [安装 mailutils](https://ghlingjun.github.io/xiaoxiao/2023/05/27/send-result-of-crontab-to-mail/)。

```shell
#!/bin/bash
#定义主机名称
SERVER_NAME="XX服务器"
# 要检测占用率的磁盘分区
DISK_PATH="/home"
LOG_PATH="/home/ethan/shell/log/monitor"
# 钉钉机器人的 access_token
DINGDING_ACCESS_TOKEN=access_token
#可配置多个邮件接收者，使用,号分割
RECIPIENT="ethan@163.com"

# 服务器性能监控，每天 10:24 执行一次
# 24 10 * * * sh /home/ethan/shell/server-monitor.sh >> /home/ethan/shell/crontab.log

DATE_STR=`date +%Y%m%d`
echo "--------------------------------------"
echo "`date +"%Y-%m-%d %H:%M:%S"` 检测服务器性能指标开始！"
# 性能阈值
DISK_THRESHOLD=80 # 磁盘使用率阈值（%）
MEM_THRESHOLD=80 # 内存使用率阈值（%）
CPU_THRESHOLD=80 # CPU使用率阈值（%）
# 定义接收通知的钉钉机器人的 Webhook URL。
DINGTALK_WEBHOOK_URL="https://oapi.dingtalk.com/robot/send?access_token=${DINGDING_ACCESS_TOKEN}"
# 获取本机IP地址输出本机的ip地址
LOCAL_IP=$(hostname -I | awk '{print $1}')
# 获取指定分区的磁盘占用率  $(df -h / | awk '/\// {print int($5)}')
DISK_USED=$(df -h $DISK_PATH | awk '/\// {print int($5)}')
# 获取当前系统的实际内存使用率（排除小数部分）
MEM_USED=$(free | awk '/Mem:/ {print int($3/$2 * 100)}')
# 获取当前系统的CPU使用率（精确到个位）。获取当前系统的实际 CPU 使用率（排除空闲 CPU 百分比的影响）
CPU_USED=$(top -bn 1 | grep "Cpu(s)" | awk '{print int(100 - $8)}')

log_path="$LOG_PATH/${DATE_STR}.log"

send_mail() {
    cat "${log_path}" | mail -s "服务器(${LOCAL_IP})性能指标" "${RECIPIENT}"
}

echo "--------------------------------------" >> ${log_path}
echo "`date +"%Y-%m-%d %H:%M:%S"`" >> ${log_path}
# 判断是否超过告警阈值
if [ $DISK_USED -gt $DISK_THRESHOLD ] || [ $MEM_USED -gt $MEM_THRESHOLD ] || [ $CPU_USED -gt $CPU_THRESHOLD ]; then
    echo "警告！性能监控发现问题！(${SERVER_NAME}) (${LOCAL_IP})" >> ${log_path}
    echo "-----------------" >> ${log_path}
    echo "磁盘已用：$DISK_USED%，其中占用率最多的前5个有：" >> ${log_path}
    echo "`du -h --max-depth=1 $DISK_PATH | sort -rh | head -n 6`" >> ${log_path}
    echo "-----------------" >> ${log_path}
    echo "内存已用：$MEM_USED%，其中占用率最多的前5个有：" >> ${log_path}
    echo "`ps -eo pid,%mem,cmd --sort=-%mem | head -n 6`" >> ${log_path}
    echo "-----------------" >> ${log_path}
    echo "CPU 已用：$CPU_USED%，其中占用率最多的前5个有：" >> ${log_path}
    echo "`ps -eo pid,%cpu,cmd --sort=-%cpu | head -n 6`" >> ${log_path}
else
    echo "服务器(${SERVER_NAME}) (${LOCAL_IP})当前资源使用情况如下：" >> ${log_path}
    echo "磁盘已用：$DISK_USED% 磁盘空间足够，无需担心" >> ${log_path}
    echo "内存已用：$MEM_USED% 内存还很充足，无需担心" >> ${log_path}
    echo "CPU已用：$CPU_USED% CPU运行正常，无需担心" >> ${log_path}
    # 每月1日将监控结果通过邮件发送给负责人
    DAY_OF_WEEK=$(date +%u)
    if [ "$DAY_OF_WEEK" -eq 1 ]; then
        send_mail
    fi
    exit 1
fi

item_monitor() {
    item_name=$1
    item_used=$2
    item_threshold=$3
    item_command=$4
    if [ ${item_used} -gt ${item_threshold} ]; then
        echo "-----------------" >> ${log_path}
        echo "警告：${item_name}资源使用率超出阈值！(${SERVER_NAME}) (${LOCAL_IP})" >> ${log_path}
        echo "已用：${item_used}% " >> ${log_path}
        echo "查看占用率靠前的资源命令如下：" >> ${log_path}
        echo "${item_command}" >> ${log_path}
        # 通过钉钉机器人发送通知消息
        curl -H "Content-Type: application/json" \
            -d '{
                "msgtype": "text",
                "text": {
                    "content": "【服务器监控】'"${item_name}"' 占用率过高：('"${item_used}"'%) on ('"${SERVER_NAME}"') ('"${LOCAL_IP}"') at '"$(date)"'"
                }
            }' $DINGTALK_WEBHOOK_URL
    fi
}

item_monitor "磁盘" ${DISK_USED} ${DISK_THRESHOLD} "du -h --max-depth=1 ${DISK_PATH} | sort -rh | head -n 6"
item_monitor "内存" ${MEM_USED} ${MEM_THRESHOLD} "ps -eo pid,%mem,cmd --sort=-%mem | head -n 6"
item_monitor "CPU" ${CPU_USED} ${CPU_THRESHOLD} "ps -eo pid,%cpu,cmd --sort=-%cpu | head -n 6"

send_mail

# 参考资料
# https://blog.csdn.net/qq_45547688/article/details/131804881
# https://blog.csdn.net/zy_9466/article/details/132298199

```
