---
title: linux利用nc工具检测
categories:
  - linux
tags:
  - centos
---

## 安装

```
#/bin/sh
yum install epel-release
yum install nmap
```

## 测试端口是否开放

```
nmap ip -p port # 测试端口
nmap ip #显示全部打开的端口
nc -v host port #端口未打开返回状态为非0
```
