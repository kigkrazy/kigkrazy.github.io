---
title: IDA导入Jni.h
categories:
 - 逆向
tags:
 - idea
 - 逆向
 - android
---

IDA 分析Android so 文件时，因为缺少JNIEnv结构定义，反编译后看起来很不友好，因此我们需要手动导入`jni.h`中相关的结构体。
### 具体操作
1. 选择ndk目录下一个平台的`${NDK_ROOT}\platforms\android-26\arch-arm\usr\include`目录下所有文件以及文件夹,复制到别的地方（一下用`${IDA_INCLUDE}`代替）。
2. 修改`jni.h`文件：
```
...
#include <sys/cdefs.h>
//第一处修改（大约在27行左右） start
//注释掉#include <stdarg.h>引用
//#include <stdarg.h>
//第一处修改 end
...
//第二处修改（大约在136行左右） start
//注释掉jobjectRefType结构体
/*
typedef enum jobjectRefType {
    JNIInvalidRefType = 0,
    JNILocalRefType = 1,
    JNIGlobalRefType = 2,
    JNIWeakGlobalRefType = 3
} jobjectRefType;*/
//第二处修改 end
...
//第三处修改（大约在497行左右） start
//注释掉jobjectRefType结构体
//jobjectRefType (*GetObjectRefType)(JNIEnv*, jobject);
//第三处修改end
...
//注释掉jobjectRefType结构体
...
//第四处修改（大约在1140行左右） start
//将 #define JNIEXPORT  __attribute__ ((visibility ("default"))) 改为 #define JNIEXPORT
//#define JNIEXPORT  __attribute__ ((visibility ("default")))
#define JNIEXPORT
//第四处修改 end
...
```

3. 回到IDA中，在菜单中选择：`Options`->`Compilter..` ，在`Include directories`中填入`D:\cygwin64\usr\include;${IDA_INCLUDE}\linux;${IDA_INCLUDE}`。
4. `File`->`Load file` -> `Parse C header file...`(或者`ctrl + F9`)，然后选择`${IDA_INCLUDE}`目录下的`jni.h`文件。
5. `Edit`->`Add struct type...`(或者键盘`Ins`)->`Add standard structure`，选择`_JNIEnv`。
