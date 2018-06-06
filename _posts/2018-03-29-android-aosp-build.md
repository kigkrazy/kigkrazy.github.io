---
title: 安卓源码(aosp)下载编译
categories:
 - android
tags:
 - Android
 - xposed
---

### 环境说明
系统：ubuntu 14.04 x64  
手机：谷歌N5

### 下载源码
#### 配置`repo`工具环境
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

#### 下载源码
```
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-5.1.1_r14
repo sync
```

#### 安装依赖
```
# 安装openjdk7
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install -y openjdk-7-jdk
# 安装相关依赖
sudo apt-get install -y git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip
```

### 开始编译
1. 初始化环境变量
```
cd ${AOSP_DIR}
source ./build/envsetup.sh
```
2. 加入驱动程序(此处假设当前目录为AOSP目录)
在[
《Driver Binaries for Nexus and Pixel Devices》](https://developers.google.com/android/drivers)找到自己手机对应的驱动，并下载到`${AOSP_DIR}`目录下。  
此处我们找到`Nexus 5 (GSM/LTE) binaries for Android 5.1.1 (LMY48M)`下的三个驱动。
3. 释放驱动文件
将第二步得到的三个驱动文件压缩包解压，得到三个sh文件，然后分别执行三个sh文件。
4. 选择编译版本
```
lunch
```
注意：nexus５对应的版本是：aosp_hammerhead_userdebug

5. 设置swap交换分区(由于编译需要大量的交换分区，此处设置4096)
```
# (count的值等于1024 x 你想要的文件大小, 8192000是8G，1024000是1G)
dd if=/dev/zero of=/opt/swap bs=1024 count=8192000
# 创建swap文件
mkswap /opt/swap
swapon /opt/swap
```
为了使每次开机时都能正常使用swap文件，所以这里需要把swap文件增加到fstab文件中。修改`/etc/fstab`，在最后一行增加如下内容：
```
 /opt/swap swap swap defaults 0 0
```
6. 开始编译
```
make
```

### 刷入机器
假设我们当前目录是AOSP，手机已经连上并且已经处于bootloader模式。可以按照下面的命令执行
```
# 1. 进入out/target/product/hammerhead目录
cd out/target/product/hammerhead
# 2. 进行双清
fastboot format cache
fastboot format userdata
# 刷入所有image
env ANDROID_PRODUCT_OUT=`pwd` fastboot flashall -w
```
`env ANDROID_PRODUCT_OUT=`pwd` fastboot flashall -w`表示刷入所有image。也可以分别单个刷入image：
```
fastboot flash recovery recovery.img
fastboot flash  boot boot.img
fastboot flash cache.img
fastboot flash system system.img
fastboot flash userdata userdata.img
```

### 配置一些环境变量方便开发
在`~/.bashrc`的末尾添加
```
export AOSP_ROOT=/usr/local/aosp
export PATH=$PATH:${AOSP_ROOT}/out/host/linux-x86/bin
```
### 用`Android Sutdio`打开AOSP

```
cd development/tools/idegen/
mm -B
cd ../../../
development/tools/idegen/idegen.sh
```
执行成功后就可以用`Android Sutdio`打开在`AOSP`根目录下的`android.ipr`文件。  
**此处还有个细节：需要将项目依赖中原来的JAVA依赖删掉，否则将出现错误引用转跳。**

### 常见问题

* 如何切换分支？
在工作目录再执行
```
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-5.1.1_r14
repo sync
```
其中`-b`后面的参数为分支版本（[Android 版本列表](https://source.android.com/source/build-numbers#source-code-tags-and-builds)）。

* `repo sync`经常卡住不动？  
这是由于BUG吧由于网络原因导致，请`ctrl + c`结束掉重新执行`repo sync`。

* 快速生成image
```
# 生成system.img
make snod
# 生成boot.img
make bootimage-nodeps
```
此处只是根据`out`已经编译好的文件进行打包，并不进行编译。

* 系统存在多个JAVA环境版本的时候jdk版本切换？
```
sudo update-alternatives --config java
sudo update-alternatives --config javac
sudo update-alternatives --config javah
sudo update-alternatives --config javap
sudo update-alternatives --config javadoc
```
如果安装了多个orical-java环境，请先到/etc/profile.d/文件夹下把jdk.\*相关文件删掉。

* 如何查看分支？
```
cd .repo/manifests
git branch
```

* 查看所有分支？
```
cd .repo/manifests
git branch -a
```

* 切换本地分支？
```
repo init -b android-6.0.1_r63
repo sync
```

### 参考资料
[谷歌说明](https://source.android.com/setup/initializing)  
[编译要求](https://source.android.com/source/requirements)  
[清华-Android 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)
