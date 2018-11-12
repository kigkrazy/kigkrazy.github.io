---
title: jekins子节点部署
categories:
  - linux
tags:
  - jekins
---

## 说明

服务端表示安装`JENKINS`的服务端机器，节点表示子节点机器。

## 从节点配置

JENKINS 的从节点配置主要有两种方式：

- SSH 方式
- JAVAWEB 方式

### SSH 方式

#### 主节点机器

##### 生成秘钥(如果已经生成则跳过此步骤)

```
ssh-keygen -t rsa
```

一路回车确定，输出结果如下：

```
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
bf:1e:4d:b3:0f:fb:8b:71:cd:ef:d3:70:69:a4:46:ff root@node1.chinasoft.com
The key's randomart image is:
+--[ RSA 2048]----+
| |
| |
| |
| . . |
| S + + .|
| . o =.*.|
| o * oo=|
| o B .E|
| .o o.+o+|
+-----------------+
```

生成`/root/.ssh/id_rsa`（私钥）和`/root/.ssh/id_rsa.pub`（公钥）文件。

##### 添加私钥到 jekins 系统中

1. 进入 jenkins 界面，`Jenkins`->`凭据`->`系统`->`全局凭据 (unrestricted)`->`添加凭据`。
2. 类型选择`SSH Username with private key`，Private Key 中输入前面生成的私钥。

#### 从节点机器

##### 安装依赖

```
yum install -y wget curl java-1.8.0-openjdk git
```

##### 添加秘钥到认证

1. 将主节点中生成的公钥`/root/.ssh/id_rsa.pub`中的内容复制下来，然后粘贴到指定用户下的认证文件中（`~/.ssh/authorized_keys`）。
2. 给认证文件赋权

   ```
   chmod 600 ~/.ssh/authorized_keys
   ```

#### jenkins 中创建节点

至此 SSH 认证配置完成，此时可以进入`jenkins`界面中创建节点。  
进入 jenkins 界面，`系统管理`->`节点管理`->`新建节点`->`${填写节点名}`->`${选择固定节点}`,然后参照如下内容进行填写

- 名字： 节点名
- 描述： 描述
- 远程目录： /root/jenkins (请注意确认认证用户有权限操作这个目录)
- 用法：尽可能使用这个节点
- 启动方式: `Launch agent agents via SSH`
- 主机：192.168.1.1
- Host Key Verification Strategy : `Manuallly trusted key Verifacation Strategy`
- 端口： 22 （点击高级后出现）
- JAVA 路径： 填写 JDK 路径 （可以不填，如果没有出现错误）

### JAVAweb 方式启动从节点

#### 配置节点

进入 jenkins 界面，`系统管理`->`节点管理`->`新建节点`->`${填写节点名}`->`${选择固定节点}`,然后参照如下内容进行填写

- 名字： 节点名
- 描述： 描述
- 远程目录： /root/jenkins (请注意确认认证用户有权限操作这个目录)
- 用法：尽可能使用这个节点
- 启动方式: `通过Java Web启动代理`

#### 从机器配置

1. 进入上一步配置的节点详细配置界面

![设置](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts/_posts_imgs/2018-11-10-jekins-subnode-tutorial-node-config.png)

2. 点击`agent.jar`下载 JAR 包，上传到从节点机器，使用界面上的命令行启动

## 问题解决

### 配置好秘钥无法认证

#### 问题描述

jenkins 节点日志中出现如下报错

```
[03/23/17 13:10:38] [SSH] Opening SSH connection to slave2.example.com:22.
[03/23/17 13:10:38] [SSH] WARNING: No entry currently exists in the Known Hosts file for this host. Connections will be denied until this new host and its associated key is added to the Known Hosts file.
Key exchange was not finished, connection is closed.
java.io.IOException: There was a problem while connecting to slave2.example.com:22
	at com.trilead.ssh2.Connection.connect(Connection.java:818)
	at com.trilead.ssh2.Connection.connect(Connection.java:687)
	at com.trilead.ssh2.Connection.connect(Connection.java:601)
	at hudson.plugins.sshslaves.SSHLauncher.openConnection(SSHLauncher.java:1265)
	at hudson.plugins.sshslaves.SSHLauncher$2.call(SSHLauncher.java:790)
	at hudson.plugins.sshslaves.SSHLauncher$2.call(SSHLauncher.java:785)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.io.IOException: Key exchange was not finished, connection is closed.
	at com.trilead.ssh2.transport.KexManager.getOrWaitForConnectionInfo(KexManager.java:93)
	at com.trilead.ssh2.transport.TransportManager.getConnectionInfo(TransportManager.java:230)
	at com.trilead.ssh2.Connection.connect(Connection.java:770)
	... 9 more
Caused by: java.io.IOException: The server hostkey was not accepted by the verifier callback
	at com.trilead.ssh2.transport.KexManager.handleMessage(KexManager.java:535)
	at com.trilead.ssh2.transport.TransportManager.receiveLoop(TransportManager.java:777)
	at com.trilead.ssh2.transport.TransportManager$1.run(TransportManager.java:489)
	... 1 more
[03/23/17 13:10:38] Launch failed - cleaning up connection
[03/23/17 13:10:38] [SSH] Connection closed.
```

#### 原因&解决方法

我从 master ssh to slave 都是没有问题的。
通过 google 发现是 ssh lib 不支持新的密码方式 ecdsa-sha2-nistp256
为此，我通过下面的命令删除 known_hosts 相关内容并重新添加认证信息。

```
# 以下操作在jenkins的master机器上
rm -rf ~/.ssh/known_hosts
ssh -o HostKeyAlgorithms=ssh-rsa slave2.example.com
```
