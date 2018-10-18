---
title: shadowsocks+chrome使用说明
categories:
  - 杂项
tags:
  - shadowsocks
---

## 一、环境

- [chrome](https://www.google.cn/intl/zh-CN/chrome/)
- [shadowsocks](https://github.com/kigkrazy/kigkrazy.github.io/raw/master/_posts/_posts_file/Shadowsocks-2.5.6.rar)

## 二、配置 shadowsocks

### 1. 下载`Shadowsocks-2.5.6`，并解压到某处。

### 2. 运行`shadowsocks`

    如果运行过程中出现：
        “.NET Framework 版本过低”请下载组件。
        微软官网下载.NET Framework 网址： https://www.microsoft.com/zh-cn/download/details.aspx?id=53344

### 3. 运行`shadowsocks`并填写好配置

- 3.1 设置如下  
  ![设置](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts_imgs/2018-10-18-ss-Instructions-setting-2.jpg)
- 3.2 在托盘右键勾选启动  
  ![设置](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts_imgs/2018-10-18-ss-Instructions-setting.png)

- 3.3 注意：

        一般我们选择 pac 模式，即访问国内网址不通过代理，全局模式为全部请求都经过代理。

## 配置 chrome

为了能更自由的配置请求代理，此处我们配合使用`chrome`浏览器。

### 1. 进入[谷歌商店](https://chrome.google.com/webstore/category/extensions)安装 SwitchyOmega 插件

### 2. 导入配置文件（[点此下载](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts_file/OmegaOptions.bak?raw=true)）

![SwitchyOmega](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts_imgs/2018-10-18-ss-Instructions-SwitchyOmega-import-2.jpg)

    可能无法直接下载配置文件，请任意一个地方新建一个文件OmegaOptions.bak，点击上面连接转跳过去，然后将网站显示的内容全部复制保存到OmegaOptions.bak，在进行导入。

### 3. 切换到指定选项

![SwitchyOmega](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts_imgs/2018-10-18-ss-Instructions-SwitchyOmega.jpg)

### 4. 如果需要特定网站走特定代理也可以在右上角插件手动选择

![SwitchyOmega](https://raw.githubusercontent.com/kigkrazy/kigkrazy.github.io/master/_posts_imgs/2018-10-18-ss-Instructions-SwitchyOmega-2.jpg)
