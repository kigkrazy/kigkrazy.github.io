---
title: jekins下部署私有git仓库
categories:
  - linux
tags:
  - jekins
---

## 说明

使用私钥-公钥的部署方式对私有 git 仓库进行自动话部署配置。

## Jenkins 服务端配置

### 生成秘钥

```
[root@root ~] ssh-keygen -t rsa
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

### 查看生成结果

#### 公钥

```
[root@root ~]  cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC3vhfbA4NdkP8g1WVyYkw93f
1PZ04nWoyFnoerC9b40jIY5px+tkugdy/RZ3/bp4hMC5yNgV1S25Tm8Rpz
BJMp7pbJz8dO+LCKqUnXq9Eh0QhsZE0xlQN+J4awy9YIGiD2nFg7k/Zz
eAtQRzNryrrPnKaWpXtg7tMGGTWBjeyLAVuqCOMU7euY94G26UmVfjD
LIJkcJqB+8dwvodW3wpmUER32qVPKZLnSYSOCfoNed+P4Eujs5PBCLu
zFvGyiDhY2Pwrk/4S11jWUa7TJItfoPXzeGc/ujaJi/o24dt8VXeFa/Rm4wy
wYLNW3TRjXy1mPpdVlob3701MMQ0bf3qPv root@root.soft.com
```

#### 私钥

```
root@root ~]   cat .ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAt74X2wODXZD/INVlcmJMPd39T2dOJ1qMhZ6HqwvW+NIyGOac
frZLoHcv0Wd/26eITAucjYFdUtuU5vEacwSTKe6Wyc/HTviwiqlJ16vRIdEIbGRN
MZUDfieGsMvWCBog9pxYO5P2c3gLUEcza8q6z5ymlqV7YO7TBhk1gY3siwFbqgjj
FO3rmPeBtulJlX4wyyCZHCagfvHcL6HVt8KZlBEd9qlTymS50mEjgn6DXnfj+BLo
7OTwQi7sxbxsog4WNj8K5P+EtdY1lGu0ySLX6D183hnP7o2iYv6NuHbfFV3hWv0Z
uMMsGCzVt00Y18tZj6XVZaG9+9NTDENG396j7wIDAQABAoIBAEHMfCR9HJTsMMDk
SmDs3JqnHWhK+UzUe0/6VmEla0VNmI0cQFyMEYcUR3Z41uulEgURf22ZLv9WDPuq
yar4r26rtynsE1avbiEpwHzQVaMDhT2zqYUg9NA/fVdgl3PtT3KgyGQFd5MgIQUN
ileGkOF6GpoGBqnOvJkJfAS5+0RHi8EZ8RcufNExgan5QF42dtKNWOEdgZIp0+WY
jmI73YEpIadQzKxjL6PCOUmFAjfuNllw3It5QLePvYYlQeWKkl8QdROOADzOxgoj
-----END RSA PRIVATE KEY-----
```

### 添加私钥到 jekins 系统中

1. 进入 jenkins 界面，`Jenkins`->`凭据`->`系统`->`全局凭据 (unrestricted)`->`添加凭据`。
2. 类型选择`SSH Username with private key`，Private Key 中输入前面生成的私钥。

## 仓库配置

进入仓库(gitlab,coding,gitee 等)，在仓库的菜单中找到类似`部署公钥`的选项，添加一个前面生成的公钥。
