---
title: 使用Gradle发布构件(Jar)到Maven中央仓库
categories:
 - gradle
tags:
 - gradle
---

## 参考文章

[使用Gradle发布构件(Jar)到Maven中央仓库(segmentfault)](https://segmentfault.com/a/1190000018026290)  
[使用的插件](https://github.com/vanniktech/gradle-maven-publish-plugin)


## 发布步骤

### 1. 注册Sonatype JIRA账号

网址：https://issues.sonatype.org/


### 2. 安装GPG，并生成秘钥

1. [GPG下载](https://link.segmentfault.com/?enc=Sa3EPeRtI%2BcfK%2B%2F3zP8mIA%3D%3D.SP1nj7lPJjI1RZM9IALkUM9sB%2Bjn5xG3DVE5BSnZMxeOGLZFizAOmdU2JgASYhbW)  

2. 使用图形化工具生成秘钥，并上传到服务器（用命令行经常失败原因未知）。

3. 导出私钥(此处用例放于C盘：C:\\secret.gpg)
```
gpg --export-secret-keys [密钥指纹] > secret.gpg
```

### 3. 配置插件相关信息（https://github.com/vanniktech/gradle-maven-publish-plugin）

#### 3.1 在用户目录下`~/.gradle/gradle.properties`添加相关秘钥以及sonatype信息

```
mavenCentralUsername=${sonatype登录账号名}
mavenCentralPassword=${sonatype登录密码}

signing.keyId=12345678
signing.password=123123
signing.secretKeyRingFile=C:\\secret.gpg
```

#### 3.2 在项目根目录`gradle.properties`添加相关插件配置(此处以我的私人项目[okhttputils](https://github.com/kigkrazy/okhttputils))
```
org.gradle.java.home=C:\\Program Files\\Android\\Android Studio\\jre
SONATYPE_HOST=S01
RELEASE_SIGNING_ENABLED=true
OSSRH_PACKAGE_GROUP=io.github.kigkrazy
GROUP=io.github.kigkrazy
VERSION_NAME=3.3.4
POM_PACKAGING=jar
POM_INCEPTION_YEAR=2018
POM_URL=https://github.com/kigkrazy/okhttputils/
POM_SCM_URL=https://github.com/kigkrazy/okhttputils/
POM_SCM_CONNECTION=scm:git:git://github.com/kigkrazy/okhttputils.git
POM_SCM_DEV_CONNECTION=scm:git:ssh://git@github.com/kigkrazy/okhttputils.git
POM_LICENCE_NAME=The Apache Software License, Version 2.0
POM_LICENCE_URL=https://www.apache.org/licenses/LICENSE-2.0.txt
POM_LICENCE_DIST=repo
POM_DEVELOPER_ID=okhttputils
POM_DEVELOPER_NAME=kigkrazy
POM_DEVELOPER_URL=https://github.com/kigkrazy/
```

#### 3.3 在上传模块的`gradle.properties`添加如下配置
```
POM_ARTIFACT_ID=okhttputils
POM_NAME =okhttputils
```

#### 3.3 在上传模块的`bulid.gradle`添加如下配置
```
...
apply plugin: "com.vanniktech.maven.publish"
...
```
#### 3.4 在项目根目录的`bulid.gradle`添加如下配置
```
buildscript {
  repositories {
    mavenCentral()
  }
  dependencies {
    classpath 'com.vanniktech:gradle-maven-publish-plugin:0.21.0'
  }
}
```

### 4. 执行发布命令
```
gradlew publish
```
