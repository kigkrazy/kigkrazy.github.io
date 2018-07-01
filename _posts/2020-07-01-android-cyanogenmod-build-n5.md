---
title: cyanogenmod(LineageOS)编译
categories:
 - android
tags:
 - cyanogenmod
 - aosp
---

本文主要说明编译一个可以在`nexus 5`上跑的`cyanogenmod-12.1`步骤。所以相关东西都是以这个为例子。

# 同步代码
## 配置`repo`工具环境
1. 安装`repo`
```
apt-get install -y curl git
mkdir -p ~/bin/
curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo > ~/bin/repo
chmod a+x ~/bin/repo
ln -s  ~/bin/repo /bin/repo
```
2. 设置镜像地址
```
#在~/.bashrc末尾添加
export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/'
```

## 下载源码（此处请根据需要下载相应版本）

### 下载`repo`描述仓库(此处我是这么叫的)
```
repo init -u repo init -u git://github.com/CyanogenMod/android.git -b cm-12.1
repo sync
```
### 修改repo描述符
找到`remote`标签配置，将骑配置为清华AOSP镜像地址：
```
  <remote  name="aosp"
           fetch="https://aosp.tuna.tsinghua.edu.cn/"
           review="android-review.googlesource.com"
           revision="refs/tags/android-5.1.1_r37" />
```
### 添加驱动与内核模块
这是比较重要的一步，如果没有这步，编译好的系统将无法进入。
```
mkdir local_manifests
touch local_manifests/lge.xml
```
往`lge.xml`文件写入内容：
```
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <project name="CyanogenMod/android_device_lge_hammerheadcaf" path="device/lge/hammerheadcaf" remote="github" />
  <project name="CyanogenMod/android_device_lge_hammerhead" path="device/lge/hammerhead" remote="github" />
  <project name="CyanogenMod/android_kernel_lge_hammerhead" path="kernel/lge/hammerhead" remote="github" />
  <project name="TheMuppets/proprietary_vendor_lge"  path="vendor/lge"  remote="github" />
</manifest>

```
### 同步代码
```
repo sync
```
此处需要一个比较长的时间。

# 编译代码
## 配置环境
此处我推荐使用我个人创建的DOCKER项目`docker-aosp-zh`
虚拟机或者真实物理机配置请参考:[安卓源码(aosp)下载编译](https://kigkrazy.github.io/android/2018/03/29/android-aosp-build/)

## 开始编译
```
source build/envsetup.sh
breakfast hammerhead
croot
brunch hammerhead
```
最后我们获得一个zip包：`cm-12.1-20180701-UNOFFICIAL-hammerhead.zip`
编译过程遇到问题请参考环境配置中提到的文章**安卓源码(aosp)下载编译**。
# 刷入机器
### 安装`twrp`（一个recovery镜像）
1. [下载](https://dl.twrp.me/hammerhead/)(此处我们下载`
twrp-3.2.1-1-hammerhead.img`)
2. 重启机器到`bootloader`模式
3. 执行recovery刷写命令
```
fastboot flash recovery 
twrp-3.2.1-1-hammerhead.img
```
### 刷写系统
1. 刷写完`twrp`，直接进入`twrp`模式
2. 点击`Advanced`->`ADB Sideload`，正确启动`adb sideload`连接
3. 执行安装(假设此时已经在`cm-12.1-20180701-UNOFFICIAL-hammerhead.zip`的目录下)
```
adb sideload cm-12.1-20180701-UNOFFICIAL-hammerhead.zip
```

# 参考文献
[Build CyanogenMod 12 for Nexus 5](http://www.sunjw.us/blog/build-cyanogenmod-12-for-nexus-5/)  
[Build for hammerhead](https://wiki.lineageos.org/devices/hammerhead/build)  

