---
title: jekins子节点部署
categories:
  - linux
tags:
  - jekins
---

## 说明

服务端表示安装`JENKINS`的服务端机器，节点表示子节点机器

## 服务端配置

## 节点配置

### 安装必要软件

```
yum install -y wget curl java-1.8.0-openjdk git
```

### 启动

```
java -jar /root/work/agent.jar -jnlpUrl http://192.168.1.12:7070/computer/test-01/slave-agent.jnlp -secret xxxxxxxx -workDir "/root/work"
```
