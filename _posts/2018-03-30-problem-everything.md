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
