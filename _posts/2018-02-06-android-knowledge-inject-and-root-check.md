---
title: Android的Xposed注入检测与Root检测
categories:
 - android
tags:
 - android
 - xposed
 - 注入检测
 - Root检测
---

## Xposed检测

### 一、方法堆栈检测

```
    public static boolean a__HasXposed(Throwable arg6__Throwable) {
        boolean v0__result = false;
        StackTraceElement[] v2 = arg6__Throwable.getStackTrace();
        int v3 = v2.length;
        int v1;
        for(v1 = 0; v1 < v3; ++v1) {
            String v4 = v2[v1].getClassName();
            if(v4 != null && (v4.contains("de.robv.android.xposed.XposedBridge"))) {
                return true;
            }
        }

        return v0__result;
    }
```

检测堆栈中是否有匹配XPOSED的包名`de.robv.android.xposed.XposedBridge`。


### 二、反射检测Xposed对象

1. 通过反射获得一个类`de.robv.android.xposed.XposedHelpers`的一个对象。
2. 通过反射获取`de.robv.android.xposed.XposedHelpers`类的一个对象`obXposedHelper`。
3. 检测`obXposedHelper`成员`fieldCache`, `methodCache`, `constructorCache`是否有支付宝包的关键字。

* `obXposedHelper`

参考文章：
[阿里系产品Xposed Hook检测机制原理分析](https://bbs.pediy.com/thread-218848.htm)

## so注入检测

### 一、maps检测
主要读取`/proc/${mypid}/maps`文件，从文件中读取模块信息，遍历有没有可疑模块。

## Root检测

### 一、检测文件是否存在
获取`default.prop`文件中`ro.secure`的值`1`，且`/system/bin/su`, `/system/xbin/su`两个文件存在。

参考文章：
[阿里系产品Xposed Hook检测机制原理分析](https://bbs.pediy.com/thread-218848.htm)


ps： 后续有新方法持续更新