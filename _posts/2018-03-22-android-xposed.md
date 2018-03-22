---
title: XPOSED遇到自定义参数数组如何Hook
categories:
 - android
tags:
 - Android
 - xposed
---

### 分析别人的APK想要hook遇到自定义参数数组如何Hook？
假设遇到如下java代码：
```
import com.kigkrazy.ErrMsg;

class LogUtil{

...
    public void d(ErrMsg[] msg){
        ....
    }
...
}
```
这时候如果我们需要用XPOSED对`LogUtil.d`进行Hook:
```
XposedHelpers.findAndHookMethod("com.kigkrazy.LogUtil", loadPackageParam.classLoader, "d", 
    //此处就是对自定对象数组类型查找的示例
    Array.newInstance(XposedHelpers.findClass("com.kigkrazy.ErrMsg", loadPackageParam.classLoader), 0).getClass(), //找到ErrMsg[]的类型
    new XC_MethodHook() {
                    @Override
                    protected void beforeHookedMethod(MethodHookParam param) throws Throwable {
                        super.beforeHookedMethod(param);
                    }

                    @Override
                    protected void afterHookedMethod(MethodHookParam param) throws Throwable {
                        super.afterHookedMethod(param);
                        Log.v("yyb-log-proto2/" + (String) param.args[0], (String)param.args[2]);
                    }
                });
```