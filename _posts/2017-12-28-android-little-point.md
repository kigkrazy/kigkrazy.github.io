---
title: Android小知识点
categories:
 - android
tags:
 - android
---

### 安卓自带ndk编译报错
>D:\zengjf\SourceCode\Android\android_app\Test>ndk-build 
C:/Users/zengjf/AppData/Local/Android/Sdk/ndk-bundle/build//../build/core/build-binary.mk:687: Android NDK: Module zengjf depends on undefined modules: android_runtime nativehelper cutils utils log
C:/Users/zengjf/AppData/Local/Android/Sdk/ndk-bundle/build//../build/core/build-binary.mk:701: *** Android NDK: Aborting (set APP_ALLOW_MISSING_DEPS=true to allow missing dependencies)    .  Stop.

* 解决方案  
 
打开NDK目录下的“build\core\build-binary.mk”文件。
找到
>ifneq ($(APP_ALLOW_MISSING_DEPS),true)

在这一行前面加上：“APP_ALLOW_MISSING_DEPS=true”。即：
>APP_ALLOW_MISSING_DEPS=true  
ifneq ($(APP_ALLOW_MISSING_DEPS),true)

###  AndroidStudio如何引入so包
参考：
>* http://blog.csdn.net/aplixy/article/details/51592035

so包应该放在相应模块（比如app模块）下的src目录下的main目录下的`jniLibs`目录。

### AndroidStudio如何引入jar包
放在对应模块的`libs`目录下。

### AndroidStudio NDK开发
参考:
>* http://www.jianshu.com/p/3a7609fc5141

### 夜神模拟器adb无法连接问题

将sdk下的adb.exe复制到夜神目录下，改名`nox_adb.exe`，用来覆盖原来的`nox_adb.exe`

### 升级到SDK25导致UIAUTOMATER无法使用
* 原因  
sdk升级到了25产生的问题。
* 解决方法  
  1. 将adb.exe 复制一份到uiautomatorviewer.bat 目录下
  2. 修改uiautomatorviewer.bat文件最后一行（改bindir=%prog_dir%为自己的platform-tools本地路径）
  
### XPOSED安卓APK多dex情况下无法找到类
* [参考文章](http://bestmk.cn/thread-511.htm)
* 原因  
现在大一点的app都有多个dex文件，如果用xposed去hook非默认dex文件的类就会发生ClassNotFoundError，要解决这个问题，我们需要拿到对应dex文件的上下文环境。
* 解决方案
Android在加载dex文件后会创建一个application类，然后会调用attach方法，attach方法的参数就是上下文context，而且attach方法是final方法，不会因为被覆盖而hook不到，拿到这个context就可以获取对应的classload，然后可以顺利hook到multidex的类。示例代码：
```
XposedHelpers.findAndHookMethod(Application.class, "attach", Context.class, new XC_MethodHook() {
                @Override
                protected void afterHookedMethod(MethodHookParam param) throws Throwable {
                    ClassLoader cl = ((Context)param.args[0]).getClassLoader();
                    Class<?> hookclass = null;
                    try {
                        hookclass = cl.loadClass("xxx.xxx.xxx");
                    } catch (Exception e) {
                        Log.e("jyy", "寻找xxx.xxx.xxx报错", e);
                        return;
                    }
                    Log.i("jyy", "寻找xxx.xxx.xxx成功");
                    XposedHelpers.findAndHookMethod(hookclass, "xxx", new XC_MethodHook(){
                        //进行hook操作
                    });
                }
            });
```

### mount系统文件夹
目前有两种版本的mount 

```
mount -o remount,rw remount /system
```

```
mount -rwo remount /system
```


#### zygote加载JAR包的时候父目录必须所有者必须为root

### 获取当前activity：
```
adb shell "su -c \"dumpsys activity \"" | grep mFoc
```

### 安卓注入debug的SAMLI代码
```
invoke-static {}, Landroid/os/Debug;->waitForDebugger()V
```

### Android ndk的ABI条件编译

| 宏 | abi | 说明 |
|:---:|:---:|:---:|
|__arm__|armeabi|会支持包集arm指令集|
|__arm__|armeabi-v7|会支持包集armeabi-v7指令集|
|__aarch64__|arm64-v8a|会支持包集armeabi-v8a指令集|
|__i386__|x86|会支持包集x86指令集|
|__x86_64__|x86_64|会支持包集x64指令集|

用法：
```
#ifdef __arm__
...
#endif
```

### Retrofit2.0项目中实践
[Retrofit2.0项目中实践](http://www.jianshu.com/p/e438594d9c93)  
[Retrofit2.0使用总结](http://www.jianshu.com/p/3e13e5d34531)  
[Android 教你一步步搭建MVP+Retrofit+RxJava网络请求框架](http://www.jianshu.com/p/7b839b7c5884)

### 更新Gradle很慢
[听说你Android Studio更新Gradle很慢](http://www.jianshu.com/p/069514ef0d06)  
[gradle镜像](http://mirrors.flysnow.org/)

### NEXUS官方原版镜像
[镜像地址](http://tieba.baidu.com/p/4212134253)

### 安卓tcpdump
[下载连接](https://www.androidtcpdump.com/android-tcpdump/downloads)

### 安卓获取启动activity以及其他信息
```
aapt dump badging ${apkFilePath}
```
运行后的结果中以下两行分别是应用包名package和入口activity名称
```
package: name=’com.estrongs.android.pop’
launchable-activity: name=’com.estrongs.android.pop.view.FileExplorerActivity’
```
注：在android sdk目录搜索可以找到aapt.exe，如果没有可以下载apktool。

### 查看包的详细信息
```
dumpsys package ${package name}
```
包括安装的APK位置
```
dumpsys package ${package name}|grep .apk
```