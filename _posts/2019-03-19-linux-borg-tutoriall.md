---
title: brog教学
categories:
 - android
tags:
 - android
---

## 安装
```
yum install borgbackup
```

## 结合[borg-backup.sh](https://github.com/Freaky/borg-backup.sh.git)进行管理

### 安装`borg-backup`命令
```
git clone https://github.com/Freaky/borg-backup.sh.git
ln -s ${borg-backup.sh-dir}/borg-backup.sh /usr/bin/borg-backup
```

### 配置
根据实际情况将下面配置复制到`/etc/borg-backup.conf`中
```
#执行文件位置如果是yum安装则位置为‘/usr/bin/borg’
BORG=/usr/local/bin/borg
# 仓库位置
# 设置远程仓库可用：TARGET=backup@backuphost:/backups/${HOSTNAME}
# 设置本地仓库则直接指定目录：TARGET=/home/backups
TARGET=backup@backuphost:/backups/${HOSTNAME}
# 压缩规则
COMPRESSION=zlib
# 密码
PASSPHRASE=123456
# 仓库修剪规则（针对所有的仓库）
# 下面规则表示：
#     24小时内保存一个
#     14天内保存一个
#     8周内保存一个
#     6个月内保存一个
PRUNE='--keep-hourly=24 --keep-daily=14 --keep-weekly=8 --keep-monthly=6'
# 设置仓库，表示设置两个仓库
BACKUPS='homes etc'
# 指定仓库地址以及相关参数，环境变量规则为：
#     BACKUP_homes 下划线后接上'BACKUPS'中对应的仓库名
BACKUP_homes='/home/freaky -e /home/freaky/Maildir/mutt-cache' # 指定homes仓库的地址以及参数
#PRUNE_homes='--keep-hourly=72 --keep-daily=365' # 针对homes仓库的修剪规则，一般我们使用总的就可以了
BACKUP_etc='/etc /usr/local/etc' # 指定etc仓库的地址以及参数
#PRUNE_etc='--keep-hourly=72 --keep-daily=365' # 针对etc仓库的修剪规则，一般我们使用总的就可以了
```