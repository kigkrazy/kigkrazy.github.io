---
title: 各种杂事问题解决方案的记录
categories:
  - everything
tags:
  - everything
---

## 虚拟机正确安装 vmtool

由于现在虚拟机更新`VMTools`的安装经常出现：无法共享文件。无法拖拽等等问题。搜索大量资料找到如下比较好的方案：  
[Files missing in /mnt/hgfs on Ubuntu VM?](https://askubuntu.com/questions/591664/files-missing-in-mnt-hgfs-on-ubuntu-vm)  
[vmware-tools-patches](https://github.com/rasa/vmware-tools-patches)  
此处建议都使用`vmware-tools-patches`进行`VMTool`的安装。

## VMWARE 占用硬盘空间越来越大的解决方法

进入 vmware 的安装目录找到`vmware-vdiskmanager.exe`。运行命令行：

```
vmware-vdiskmanager.exe -k "xxx.vmdk"
```

## `ubuntu`,`mint`下`sudo`免密码配置

在`/etc/sudoers`的末尾添加如下配置：

```
${user_name} ALL=(ALL) NOPASSWD: ALL
```

## 添加 docker 权限给当前用户，使 docker 命令免 sudo

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

## windows 重置网络

命令行执行

```
netsh winsock reset
```

## 解除谷歌浏览器 Chrome 无法安装扩展插件的限制

1. 将谷歌应用商店（Chrome Store）外的扩展插件安装程序，下载后，保存在本地硬盘上。将保存下来的*.crx 文件或*.js 文件直接拖拽到浏览器的“扩展程序”页面（即在谷歌浏览器地址栏输入：chrome://chrome/extensions/）。

2. 在 Chrome 的桌面快捷方式上选择右键——属性, 在"目标" 后增加参数
   ```
   --enable-easy-off-store-extension-install
   ```

##  解决chrome提示您的连接不是私密连接的方法
在 Chrome 的桌面快捷方式上选择右键——属性, 在"目标" 后增加参数
```
--test-type --ignore-certificate-errors
```

## UIAUTOMATOR 无法获取节点树

```
Error obtaining UI hierarchy Error while obtaining UI hierarchy XML file: com.android.ddmlib.SyncExc
```

解决方法：

```
adb root
```

## 解决 npm 编译错误

```
ERROR in Missing binding /home/linux-haow/文档/seeker/client/node_modules/node-sass/vendor/linux-x64-48/binding.node
[1] Node Sass could not find a binding for your current environment: Linux 64-bit with Node.js 6.x
[1][1] Found bindings for the following environments:
[1]   - Linux 64-bit with Node.js 4.x
[1][1] This usually happens because your environment has changed since running `npm install`.
[1] Run `npm rebuild node-sass` to build the binding for your current environment.
```

解决方案：

```
# At first going into project folder. Then write bellow code

npm rebuild node-sass
```

## LINUX 下 IDEA 无法记住 database 密码

按照如下进入设置进行设置`File | Settings | Appearance & Behavior | System Settings | Passwords`。进入配置界面后选择`In KeePass`

## git diff “old mode 100755 new mode 100644”

```
git config --add core.filemode false
```

## git 出现问题`fatal: The remote end hung up unexpectedly`
问题一般是缓存设置太小
```
git config --global http.postBuffer 524288000
git config --global http.maxRequestBuffer 500M
git config --global core.compression 0
```