---
title: centos7安装初始化工具环境
categories:
  - linux
tags:
  - centos
---

```
#/bin/sh
yum install epel-release
yum install vim
yum install nmap
yum groupinstall -y "Development tools"
yum -y install python-pip #安装pip
# yum install python34 python3-pip # 安装python3.4,需要可以去掉注释
```
