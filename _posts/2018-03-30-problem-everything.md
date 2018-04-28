---
title: 各种杂事问题解决方案的记录
categories:
 - everything
tags:
 - everything
---

## 虚拟机正确安装vmtool
由于现在虚拟机更新`VMTools`的安装经常出现：无法共享文件。无法拖拽等等问题。搜索大量资料找到如下比较好的方案：  
[Files missing in /mnt/hgfs on Ubuntu VM?](https://askubuntu.com/questions/591664/files-missing-in-mnt-hgfs-on-ubuntu-vm)  
[vmware-tools-patches](https://github.com/rasa/vmware-tools-patches)  
此处建议都使用`vmware-tools-patches`进行`VMTool`的安装。

## VMWARE 占用硬盘空间越来越大的解决方法
进入vmware的安装目录找到`vmware-vdiskmanager.exe`。运行命令行：
```
vmware-vdiskmanager.exe -k "xxx.vmdk"  
```
## `ubuntu`,`mint`下`sudo`免密码配置
在`/etc/sudoers`的末尾添加如下配置：
```
${user_name} ALL=(ALL) NOPASSWD: ALL
```

## 添加docker权限给当前用户，使docker命令免sudo
```
#添加docker group
sudo groupadd docker
#将当前用户添加到docker组
sudo gpasswd -a ${USER} docker
#重启docker服务
sudo service docker restart
```

## `linux mint`重置桌面设置（不小心误操作了桌面，进行重置）
```
gsettings reset-recursively org.cinnamon # THIS IS FOR CINNAMON

gsettings reset-recursively org.mate.panel # THIS IS FOR MATE
```

## windows重置网络
命令行执行
```
netsh winsock reset
```
