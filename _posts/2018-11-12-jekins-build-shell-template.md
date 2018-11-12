---
title: jekins构建脚本模板
categories:
  - linux
tags:
  - jekins
---

```
#/bin/sh

# 本脚本仅适用于CentOS
WORK_DIR=`pwd` #工作目录
echo 'install all dependences'
yum install -y epel git java-1.8.0-openjdk java-1.8.0-openjdk-devel vim wget curl unzip

echo 'install gradle'
cd /usr/local/

# 获取gradle,并安装
# 来源有
# https://services.gradle.org/distributions/
if [ ! -f "/usr/local/gradle/bin/gradle" ];then
  rm -rf /usr/local/gradle/bin/gradle
  rm -rf gradle-4.10.2-bin.zip
  wget https://services.gradle.org/distributions/gradle-4.10.2-bin.zip
  unzip -o gradle-4.10.2-bin.zip
  mv gradle-4.10.2 gradle
  # 设置gradle环境变量
  export GRADLE_HOME=/usr/local/gradle
  export PATH=$GRADLE_HOME/bin:$PATH
fi
cd $WORK_DIR

# 编译

echo 'build project'
./gradlew release

# 启动

echo "build and run success"
```
