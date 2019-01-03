---
title: cetnos7部署NEXUS3
categories:
  - 项目管理
tags:
  - NEXUS3
---

## 环境

- centos 7.x

## 安装

根据自己实际情况修改目录`/home/workspace/nexus3`为`nexus3`工作目录。

```
yum install -y epel-release
yum install -y docker
mkdir /home/workspace/nexus3 && chown -R 200 /home/workspace/nexus3
docker run -d -p 7670:8081 --restart=always --name nexus -v /home/workspace/nexus3:/nexus-data sonatype/docker-nexus3
```

安装完成之后初始账号为`admin`，密码`admin123`。

## 创建私有仓库并赋权

### 创建仓库

1. 进入仓库管理界面  
   ![图1](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts/_posts_imgs/2019-01-03-nexus3-deploy-1.png)

2. 点击左上角的`Create repository`

3. 选择`maven2(hosted)`

4. 在`Name`输入框输入对应的名字（例如:`test-release`）

5. `Version policy`选择`Release`

6. 点击最下面的`Create repository`

> 按照上面步骤我们已经创建了一个 release 版本的仓库，一般我们会创建 2 个私有仓库——一个`release`和`snapshot`。所以我们按照上面步骤在创建一个`snapshot`
> 版本的仓库`test-snapshot`(在第`5`步选择`Snapshot`)。  
> 关于两个库的区别，可以参照如下描述：  
>maven2会根据模块的版本号(pom文件中的version)中是否带有-SNAPSHOT来判断是快照版本还是正式版本。如果是快照版本，那么在mvn deploy时会自动发布到快照版本库中，会覆盖老的快照版本，而在使用快照版本的模块，在不更改版本号的情况下，直接编译打包时，maven会自动从镜像服务器上下载最新的快照版本。如果是正式发布版本，那么在mvn deploy时会自动发布到正式版本库中，而使用正式版本的模块，在不更改版本号的情况下，编译打包时如果本地已经存在该版本的模块则不会主动去镜像服务器上下载。

### 创建角色(`Role`)

1. 进入创建角色界面

   选择右边选择树`Securty` -> `Roles` -> `Create role` -> `Nexus Role`。

2. 创建角色  
   ![图2](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts/_posts_imgs/2019-01-03-nexus3-deploy-2.png)

### 创建用户（`User`）

1. 进入创建角色界面

   选择右边选择树`Securty` -> `Users` -> `Create user`。

2. 创建用户  
   ![图3](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts/_posts_imgs/2019-01-03-nexus3-deploy-3.png)

> 至此我们创建完仓库，并创建相关管理对象了。

## 发布库到仓库
1. 设置gradle脚本
完整build.gradle示例如下：
```
plugins {
    id 'java'
}

group 'reizx'
version '1.2'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
}

// region 上传例子
apply plugin: 'maven-publish'

publishing {
    publications {
        maven(MavenPublication) {
            artifactId 'prl'
            from components.java
            groupId 'reizx'
            version project.version
        }
    }
    repositories {
        maven {
            credentials {
                username 'test'
                password '1111111'
            }
            if (!project.version.endsWith('-SNAPSHOT')) {
                url "${nexusUrl}/repository/test-releases"
            } else {
                url "${nexusUrl}/repository/test-snapshots"
            }
        }
    }
}
// endregion
```

2. 发布
```
gradlew publish
```

## 引用私有库内容
1. 引入仓库
    ```
    repositories {
        jcenter()
        maven {
            // 配置用户名和密码
            credentials { username 'test' password '111111' }
            // 配置仓库地址（获取方式见上文）
            url "http://localhost:8081/repository/gcssloop-central/"
        }
    }
    ```

2. 引入库
    ```
    // <组名/包名>:<项目名>:<版本号>，例如：
    compile 'com.gcssloop.demo:uploader:1.0.0'
    ```