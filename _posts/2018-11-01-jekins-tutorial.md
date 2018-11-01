---
title: jekins安装部署说明
categories:
  - linux
tags:
  - jekins
---

## 环境

- openjdk1.8
- CentOS7
- git
- maven
- npm

安装环境

```
yum install -y git
yum install -y maven
yum install -y npm
yum install -y gradle
```

## 安装`jdk`

```
yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel
```

获取`JAVA_HOME`路径

```
dirname $(readlink $(readlink $(which java)))
```

配置`JAVA_HOME`路径到`~/.bashrc`：

```
export JAVA_HOME=上面dirname命令获取到的路径，不要bre/bin最后这段
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
```

## `jekins`

### 安装`jekins`

此处有两种版本可以安装

#### 安装最新版`jenkins`

```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
```

#### 安装稳定版版`jenkins`

```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
sudo rpm --import http://pkg.jenkins-ci.org/redhat-stable/jenkins-ci.org.key
sudo yum install jenkins
```

### 修改`jekins`配置

#### 编辑 jenkins 的/etc/sysconfig/jenkins 配置文件，修改端口、系统运行账户

```
JENKINS_USER="root" # 用户
JENKINS_PORT="7070" #端口
```

#### 启动

```
service jenkins start # 启动
sudo chkconfig jenkins on # 设置开机启动
```

### 问题解决

#### 日志文件过大

安装插件: `Logfilesizechecker`和`Build-timeout`

#### 忘记密码

编辑`{$JENKINS_HOME}/users/admin/config.xml`，找到如下内容

```
<hudson.security.HudsonPrivateSecurityRealm_-Details>
      <passwordHash>#jbcrypt:$xxxxxxxxxx</passwordHash>
</hudson.security.HudsonPrivateSecurityRealm_-Details>
```

将`passwordHash`标签中的内容换为

```
#jbcrypt:$2a$10$DdaWzN64JgUtLdvxWIflcuQu2fgrrMSAMabF5TSrGK5nXitqK9ZMS
```

这时候密码就变为`111111`。用账户名`admin`，和密码`111111`登陆后修改密码即可。
