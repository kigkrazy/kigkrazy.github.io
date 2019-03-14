---
title: linux同步时间
categories:
 - android
tags:
 - android
---

执行下面命令:
```
查看时间
date 
# 安装同步时间工具
yum install ntpdate
# 设置时区为上海
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 同步时间
ntpdate cn.pool.ntp.org   
```