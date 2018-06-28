---
title: 阿里移动数据安全UTDID分析
categories:
 - android
tags:
 - Android
 - 逆向
---

## 主要函数介绍
`publicstatic String getUtdid(Context arg2)`是utdid库对外调用的接口, 在`com.ta.utdid2.device.UTDevice`类里，主要功能`com.ta.utdid2.device.UTUtdid.getValue()`。
## `com.ta.utdid2.device.UTUtdid.getValue()`主要信息存储位置

1. 从Setting.System读取"mqBRboGZkQPcAkyk"，如果有值，则直接作为utid使用，如果获取失败，继续读"dxCRMxhQkdGePGnp",这里面的值是utid使用aes加密后，再进行base64编译的结果，对其进行解密，即可得到utid,并将"mqBRboGZkQPcAkyk"的值，修改正确。失败则进行下一步
2. 从name为"Alvin2"的SharedPreferences读取UTDID做为utdid,对其进行读取校验，失败就从name为"ContextData"的SharedPreferences里读取K_1171477665，对读取值进行解密，如果解密后的结果正确，则将其作为utdid返回
3. 从文件中读取`/sdcard/.UTSystemConfig`以及`/sdcard/.DataStorage`
4. 以上三个地方读取失败，则进入生成步骤，生成位置为`com.ta.utdid2.device.UTUtdid. _generateUtdid()`
```
    private final byte[] _generateUtdid() {
        String imei;
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        int currentTimeMillis = (int) (System.currentTimeMillis() / 1000);
        int nextInt = new Random().nextInt();
        byte[] bytes = IntUtils.getBytes(currentTimeMillis);
        byte[] bytes2 = IntUtils.getBytes(nextInt);
        byteArrayOutputStream.write(bytes, 0, 4);
        byteArrayOutputStream.write(bytes2, 0, 4);
        byteArrayOutputStream.write(3);
        byteArrayOutputStream.write(0);
        try {
            imei = PhoneInfoUtils.getImei(this.mContext);
        } catch (Exception e) {
            imei = new Random().nextInt();
        }
        byteArrayOutputStream.write(IntUtils.getBytes(StringUtils.hashCode(imei)), 0, 4);
        byteArrayOutputStream.write(IntUtils.getBytes(StringUtils.hashCode(_calcHmac(byteArrayOutputStream.toByteArray()))));
        return byteArrayOutputStream.toByteArray();
    }
```
生成的时候会引入两个随机值，一个是时间，一个是Random，然后跟IMEI串联起来，IMEI如果获取不成功就再用一个Random.这里的生成因子其实就是两个随机值外加IMEI。

**IMEI的获取是相当不稳定的。部分安全软件会拦截此值的获取，小米手机很早就可以设置让此值返回为空。6.0系统以后，在targetSDKVersion为23的条件下，IMEI默认也是获取不到的。**

## 参考
[阿里系UTDID库生成唯一性ID分析](https://blog.csdn.net/justfwd/article/details/50549971)