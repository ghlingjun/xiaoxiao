---
title: 服务器性能监控及钉钉通知方案
comments: true
categories:
  - 运维
tags: 
  - message
date: 2024-07-12 16:57:17
updated: 2024-07-12 16:57:17
---

本方案实现思路是通过 shell 脚本查询服务器相关性能指标，并将性能指标记录下来，如果发现指标超出预设的阈值，则发送钉钉通知给相关人员。具体实现思路如下：

# 创建脚本目录
使用如下命令创建所需目录：
`mkdir -p /home/ethan/shell/log/monitor`
使用`-p`选项，这样如果上层目录不存在，它会自动创建它们。其中用户目录`ethan`根据实际修改。

# 创建监控脚本
在`/home/ethan/shell`目录下创建 shell 脚本`server-monitor.sh`，内容参考附件【监控脚本】。
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
脚本中常量`SERVER_NAME、DINGDING_ACCESS_TOKEN、DISK_PATH、LOG_PATH`值根据实际进行修改。

```shell
#!/bin/bash

#定义主机名称
SERVER_NAME="XX服务器"
# 钉钉机器人的 access_token
DINGDING_ACCESS_TOKEN=access_token
# 要检测占用率的磁盘分区
DISK_PATH="/home"
LOG_PATH="/home/ethan/shell/log/monitor"

DATE_STR=`date +%Y%m%d`
# 服务器性能监控，每天 10:24 执行一次
# 24 10 * * * sh /home/ethan/shell/server-monitor.sh >> /home/ethan/shell/crontab.log
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

echo "--------------------------------------" >> $LOG_PATH/$DATE_STR.log
echo "`date +"%Y-%m-%d %H:%M:%S"`" >> $LOG_PATH/$DATE_STR.log
# 判断是否超过告警阈值
if [ $DISK_USED -gt $DISK_THRESHOLD ] || [ $MEM_USED -gt $MEM_THRESHOLD ] || [ $CPU_USED -gt $CPU_THRESHOLD ]; then
    echo "警告！性能监控发现问题！" >> $LOG_PATH/$DATE_STR.log
    echo "磁盘已用：$DISK_USED%" >> $LOG_PATH/$DATE_STR.log
    echo "内存已用：$MEM_USED%" >> $LOG_PATH/$DATE_STR.log
    echo "CPU 已用：$CPU_USED%" >> $LOG_PATH/$DATE_STR.log
else
    echo "磁盘已用：$DISK_USED% 磁盘空间足够，无需担心" >> $LOG_PATH/$DATE_STR.log
    echo "内存已用：$MEM_USED% 内存还很充足，无需担心" >> $LOG_PATH/$DATE_STR.log
    echo "CPU已用：$CPU_USED% CPU运行正常，无需担心" >> $LOG_PATH/$DATE_STR.log
    exit 1
fi

if [ ${DISK_USED} -gt $DISK_THRESHOLD ]; then
    echo "-----------------" >> $LOG_PATH/$DATE_STR.log
    echo "警告：磁盘空间快不足！" >> $LOG_PATH/$DATE_STR.log
    echo "已用：$DISK_USED% " >> $LOG_PATH/$DATE_STR.log
    echo "其中占用率最多的前5个有：" >> $LOG_PATH/$DATE_STR.log
    echo "`du -h --max-depth=1 $DISK_PATH | sort -rh | head -n 6`" >> $LOG_PATH/$DATE_STR.log
    # 如果磁盘使用情况过高，通过钉钉机器人发送通知消息
    curl -H "Content-Type: application/json" \
        -d '{
            "msgtype": "text",
            "text": {
                "content": "【服务器监控】磁盘占用率过高：('"$DISK_USED"'%) on ('"$SERVER_NAME"') ('"$LOCAL_IP"') at '"$(date)"'"
            }
        }' $DINGTALK_WEBHOOK_URL
fi

if [ ${MEM_USED} -gt $MEM_THRESHOLD ]; then
    echo "-----------------" >> $LOG_PATH/$DATE_STR.log
    echo "警告：内存使用率过高！" >> $LOG_PATH/$DATE_STR.log
    echo "已用：$MEM_USED% " >> $LOG_PATH/$DATE_STR.log
    echo "其中使用率最多的前5个有：" >> $LOG_PATH/$DATE_STR.log
    echo "`ps -eo pid,%mem,cmd --sort=-%mem | head -n 6`" >> $LOG_PATH/$DATE_STR.log
    # 如果内存用量过高，通过钉钉机器人发送通知消息
    curl -H "Content-Type: application/json" \
        -d '{
            "msgtype": "text",
            "text": {
                "content": "【服务器监控】内存占用率过高：('"$MEM_USED"'%) on ('"$SERVER_NAME"') ('"$LOCAL_IP"') at '"$(date)"'"
            }
        }' $DINGTALK_WEBHOOK_URL
fi

if [ ${CPU_USED} -gt $CPU_THRESHOLD ]; then
    echo "-----------------" >> $LOG_PATH/$DATE_STR.log
    echo "警告：CPU 使用率过高！" >> $LOG_PATH/$DATE_STR.log
    echo "已用：$CPU_USED% " >> $LOG_PATH/$DATE_STR.log
    echo "其中使用率最多的前5个有：" >> $LOG_PATH/$DATE_STR.log
    echo "`ps -eo pid,%cpu,cmd --sort=-%cpu | head -n 6`" >> $LOG_PATH/$DATE_STR.log
    # 如果内存用量过高，通过钉钉机器人发送通知消息
    curl -H "Content-Type: application/json" \
        -d '{
            "msgtype": "text",
            "text": {
                "content": "【服务器监控】CPU 使用率过高：('"$CPU_USED"'%) on ('"$SERVER_NAME"') ('"$LOCAL_IP"') at '"$(date)"'"
            }
        }' $DINGTALK_WEBHOOK_URL
fi

# 参考资料
# https://blog.csdn.net/qq_45547688/article/details/131804881
# https://blog.csdn.net/zy_9466/article/details/132298199

```
