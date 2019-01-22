---
title: supervisor的简明部署教程
categories:
  - 运营维护
tags:
  - supervisor
---

## 环境

- centos 7.x

```
yum install -y epel git java-1.8.0-openjdk java-1.8.0-openjdk-devel vim wget curl unzip lrzsz supervisor

echo ========================= config supervisord =========================
echo_supervisord_conf > /etc/supervisord.conf
# 输出supervisord的基本配置
cat >> /etc/supervisord.conf<<EOF

[include]
files = /etc/supervisor/*.ini
EOF

mkdir -p /etc/supervisor/
# 创建日志目录
mkdir -p /root/logs/supervisor
# 设置子进程配置，可以多配置几个，也可根据需要删除
# 配置只需要 
# [program:xxx] 任务名
# command 启动命令
# stdout_logfile 普通日志文件
# stderr_logfile 错误日志文件

# 配置子模块
cat > /etc/supervisor/sub_program.ini<<EOF
[program:xxx]
command=java -jar /root/xxx.jar
user=root
priority=999
numprocs=1
autostart=true
autorestart=true
startsecs=1
startretries=2
stopsignal=TERM
stdout_logfile=/root/logs/supervisor/xxx.log
stderr_logfile=/root/logs/supervisor/xxx.err
stdout_logfile_maxbytes=50MB
stdout_logfile_backups=10
stopasgroup=true
EOF
echo ========================= config supervisord over =========================

# 设置开机启动
echo 'supervisord -c /etc/supervisord.conf' >> /etc/rc.local
chmod +x /etc/rc.local
# 启动
supervisord -c /etc/supervisord.conf
```