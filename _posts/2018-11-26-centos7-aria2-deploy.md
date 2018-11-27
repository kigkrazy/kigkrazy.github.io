---
title: cetnos7部署ARIA2
categories:
  - dialy
tags:
  - aria2
---

## 环境

- centos7

## 安装

```
yum install -y epel-release aria2
```

## 配置

根据需要修改下列配置，并写到`/etc/aria2.conf`。几个比较特殊的字段

- `dir` 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
- `save-session` 在 Aria2 退出时保存`错误/未完成`的下载任务到会话文件
- `input-file` 从会话文件中读取下载任务
- `bt-tracker` 种子追踪服务器（后面会提到如何更新）

上面几个字段请根据实际情况进行修改

```
## '#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改 ##
## 被注释的选项填写的是默认值, 建议在需要修改时再取消注释  ##

## 文件保存相关 ##

# 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
dir=/home/aria2/download
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
#disk-cache=32M
# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
#file-allocation=none
# 断点续传
continue=true

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5
#max-concurrent-downloads=5
# 同一服务器连接数, 添加时可指定, 默认:1
max-connection-per-server=5
# 最小文件分片大小, 添加时可指定, 取值范围1M -1024M, 默认:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 添加时可指定, 默认:5
#split=5
# 整体下载速度限制, 运行时可修改, 默认:0
#max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0
#max-download-limit=0
# 整体上传速度限制, 运行时可修改, 默认:0
#max-overall-upload-limit=0
# 单个任务上传速度限制, 默认:0
#max-upload-limit=0
# 禁用IPv6, 默认:false
#disable-ipv6=true
# 连接超时时间, 默认:60
#timeout=60
# 最大重试次数, 设置为0表示不限制重试次数, 默认:5
#max-tries=5
# 设置重试等待的秒数, 默认:0
#retry-wait=0

## 进度保存相关 ##

# 从会话文件中读取下载任务
input-file=/home/aria2/aria2.session
# 在Aria2退出时保存`错误/未完成`的下载任务到会话文件
save-session=/home/aria2/aria2.session
# 定时保存会话, 0为退出时才保存, 需1.16.1以上版本, 默认:0
#save-session-interval=60

## RPC相关设置 ##

# 启用RPC, 默认:false
enable-rpc=true
# 允许所有来源, 默认:false
rpc-allow-origin-all=true
# 允许非外部访问, 默认:false
rpc-listen-all=true
# 事件轮询方式, 取值:[epoll, kqueue, port, poll, select], 不同系统默认值不同
#event-poll=select
# RPC监听端口, 端口被占用时可以修改, 默认:6800
#rpc-listen-port=6800
# 设置的RPC授权令牌, v1.18.4新增功能, 取代 --rpc-user 和 --rpc-passwd 选项
rpc-secret=905639
# 设置的RPC访问用户名, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-user=<USER>
# 设置的RPC访问密码, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-passwd=<PASSWD>
# 是否启用 RPC 服务的 SSL/TLS 加密,
# 启用加密后 RPC 服务需要使用 https 或者 wss 协议连接
#rpc-secure=true
# 在 RPC 服务中启用 SSL/TLS 加密时的证书文件,
# 使用 PEM 格式时，您必须通过 --rpc-private-key 指定私钥
#rpc-certificate=/path/to/certificate.pem
# 在 RPC 服务中启用 SSL/TLS 加密时的私钥文件
#rpc-private-key=/path/to/certificate.key

## BT/PT下载相关 ##

# 当下载的是一个种子(以.torrent结尾)时, 自动开始BT任务, 默认:true
#follow-torrent=true
# BT监听端口, 当端口被屏蔽时使用, 默认:6881-6999
listen-port=51413
# 单个种子最大连接数, 默认:55
#bt-max-peers=55
# 打开DHT功能, PT需要禁用, 默认:true
enable-dht=true
# 打开IPv6 DHT功能, PT需要禁用
enable-dht6=true
# DHT网络监听端口, 默认:6881-6999
#dht-listen-port=6881-6999
# 本地节点查找, PT需要禁用, 默认:false
bt-enable-lpd=true
# 种子交换, PT需要禁用, 默认:true
enable-peer-exchange=true
# 每个种子限速, 对少种的PT很有用, 默认:50K
#bt-request-peer-speed-limit=50K
# 客户端伪装, PT需要
peer-id-prefix=-TR2770-
#user-agent=Transmission/2.77
user-agent=Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36
# 当种子的分享率达到这个数时, 自动停止做种, 0为一直做种, 默认:1.0
seed-ratio=0
# 强制保存会话, 即使任务已经完成, 默认:false
# 较新的版本开启后会在任务完成后依然保留.aria2文件
#force-save=false
# BT校验相关, 默认:true
#bt-hash-check-seed=true
# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=true
# 保存磁力链接元数据为种子文件(.torrent文件), 默认:false
bt-save-metadata=true
bt-tracker=udp://tracker.coppersurfer.tk:6969/announce,udp://tracker.internetwarriors.net:1337/announce,http://tracker.internetwarriors.net:1337/announce,udp://tracker.opentrackr.org:1337/announce,udp://9.rarbg.to:2710/announce,udp://exodus.desync.com:6969/announce,udp://explodie.org:6969/announce,http://explodie.org:6969/announce,udp://tracker1.itzmx.com:8080/announce,udp://ipv4.tracker.harry.lu:80/announce,http://tracker3.itzmx.com:6961/announce,http://tracker1.itzmx.com:8080/announce,udp://denis.stalker.upeer.me:6969/announce,udp://tracker.torrent.eu.org:451/announce,udp://tracker.port443.xyz:6969/announce,udp://retracker.lanta-net.ru:2710/announce,udp://open.stealth.si:80/announce,udp://open.demonii.si:1337/announce,http://tracker.port443.xyz:6969/announce,udp://tracker.tiny-vps.com:6969/announce,udp://tracker.iamhansen.xyz:2000/announce,udp://zephir.monocul.us:6969/announce,https://tracker.fastdownload.xyz:443/announce,https://opentracker.xyz:443/announce,http://torrent.nwps.ws:80/announce,http://opentracker.xyz:80/announce,http://open.trackerlist.xyz:80/announce,udp://tracker.cyberia.is:6969/announce,udp://thetracker.org:80/announce,http://tracker.city9x.com:2710/announce,http://open.acgnxtracker.com:80/announce,udp://tracker4.itzmx.com:2710/announce,udp://tracker2.itzmx.com:6961/announce,udp://tracker.uw0.xyz:6969/announce,udp://tracker.tvunderground.org.ru:3218/announce,udp://tracker.swateam.org.uk:2710/announce,udp://tracker.filepit.to:6969/announce,udp://tracker.btsync.gq:233/announce,udp://torrentclub.tech:6969/announce,udp://amigacity.xyz:6969/announce,http://tracker4.itzmx.com:2710/announce,http://tracker2.itzmx.com:6961/announce,http://tracker.tvunderground.org.ru:3218/announce,http://tracker.torrentyorg.pl:80/announce,http://tracker.cypherpunks.ru:6969/announce,http://tracker.btsync.gq:233/announce,http://torrentclub.tech:6969/announce,http://t.nyaatracker.com:80/announce,http://retracker.mgts.by:80/announce,http://open.acgtracker.com:1096/announce,http://amigacity.xyz:6969/announce,http://0d.kebhana.mx:443/announce,wss://tracker.openwebtorrent.com:443/announce,wss://tracker.fastcast.nz:443/announce,wss://tracker.btorrent.xyz:443/announce,wss://ltrackr.iamhansen.xyz:443/announce,udp://tracker.justseed.it:1337/announce,udp://pubt.in:2710/announce,udp://packages.crunchbangplusplus.org:6969/announce,udp://bittracker.ru:6969/announce,http://tracker.tfile.me:80/announce.php,http://tracker.tfile.me:80/announce,http://tracker.tfile.co:80/announce,http://share.camoe.cn:8080/announce,http://private.minimafia.nl:443/announce,http://prestige.minimafia.nl:443/announce,http://peersteers.org:80/announce,http://fxtt.ru:80/announce,http://bittracker.ru:80/announce
```

## 启动

```
aria2c --conf-path=/etc/aria2.conf -D
```

## 扩展

### 如何管理 aria2？

此处有几个推荐:

- [AriaNg](https://github.com/mayswind/AriaNg)
- [yaaw](http://yaaw.qiniudn.com/)
- [webui-aria2](https://github.com/ghostry/webui-aria2)

### 如何外网离线？

自备一台外网服务，然后采用 SSH 反射代理，进行内网穿透。

### BT/磁力下载速度为 0

参考文章：[解决 Aria2 BT 下载速度慢没速度的问题](http://www.senra.me/solutions-to-aria2-bt-metalink-download-slowly)

文章主要提到两点：

1. 更新配置中的`bt-tracker`字段
2. 在抗 DMCA 的服务器上请把如下选项打开
   ```
   enable-dht=true
   bt-enable-lpd=true
   enable-peer-exchange=true
   ```

### 自动更新`bt-tracker`

1. 执行下列命令下载自动更新脚本：

   ```
   cd /usr/bin &&
   wget -O trackers-list-aria2.sh --no-check-certificate https://storage.liyuans.com/09.Shell/trackers-list-aria2.sh &&
   chmod +x /root/trackers-list-aria2.sh
   ```

2. 简单修改脚本，将脚本中的`/root/.aria2/aria2.conf`修改成自己配置文件的位置，本文中的配置文件位置为`/etc/aria2.conf`。
3. 执行更新
   ```
   ./trackers-list-aria2.sh
   ```

自动更新脚本（trackers-list-aria2.sh）内容(此处主要做个备份)

```
#!/bin/bash
/usr/sbin/service aria2 stop
list=`wget -qO- https://raw.githubusercontent.com/ngosang/trackerslist/master/trackers_all.txt|awk NF|sed ":a;N;s/\n/,/g;ta"`
if [ -z "`grep "bt-tracker" /root/.aria2/aria2.conf`" ]; then
    sed -i '$a bt-tracker='${list} /root/.aria2/aria2.conf
    echo add......
else
    sed -i "s@bt-tracker.*@bt-tracker=$list@g" /root/.aria2/aria2.conf
    echo update......
fi
```
