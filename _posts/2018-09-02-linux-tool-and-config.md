---
title: linux常用工具与配置
categories:
 - linux
tags:
 - linux
 - systemd
---

此篇文章主要介绍一些linux下的常用工具或配置。

## systemd添加`rc.local`服务（所有操作均在root权限下操作）
1. 创建或修改`/etc/systemd/system/rc-local.service`或者`/usr/lib/systemd/system/rc-local.service`（建议在这里）为如下内容
```
[Unit]
 Description=/etc/rc.local Compatibility
 ConditionPathExists=/etc/rc.local

[Service]
 Type=forking
 ExecStart=/etc/rc.local start
 TimeoutSec=0
 StandardOutput=tty
 RemainAfterExit=yes
 SysVStartPriority=99

[Install]
 WantedBy=multi-user.target
```   

2. 创建并赋予`/etc/rc.local`权限
```
touch /etc/rc.local
chmod +x /etc/rc.local
```
3. 此时可以在`/etc/rc.local`添加开机启动命令了
```
sudo systemctl enable rc-local # 开机启动
sudo systemctl disable rc-local # 禁止开机启动
```
# ssh携带密码登录-sshpass


# 一个系统状态查看工具`gtop`
```
yum install npm
# 安装cnpm
npm install cnpm -g
# 更新
npm install npm -g 
# 源切换工具
npm install nrm -g
cnpm install gtop -g
```