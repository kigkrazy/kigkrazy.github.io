---
title: arch下virtualbox安装
categories:
  - linux
tags:
  - arch
---

## 安装

### 1. 安装主要模块

```
yum install vitualbox
```

### 2. 安装驱动模块

```
命令会出现找不到模块的提示。
解决方法是，根据uname -r 找到内核版本，我的是4.14.34-1-MANJARO
然后安装对应的模块sudo pacman -S linux414-virtualbox-host-modules，即可解决问题。

最后，一点，就是在安装virtualbox时，会有选择模块的步骤，不要选择默认的，此时应该先查看一下系统版本，再选择相应的模块安装。另外，内核升级后，因为加载模块时，会在对应的系统版本文件夹（/lib/modules/4.14.34-1-MANJARO）里面寻找，所以也需要安装版本对应的模块，不然会导致模块找不到而无法开机，那就麻烦了。
```
