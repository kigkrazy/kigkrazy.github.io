---
title: 史上最简单的GreenDao的教程
categories:
 - android
tags:
 - android
 - GreenDao
---

### GreenDao 介绍
>1. 性能高，号称Android最快的关系型数据库  
>2. 内存占用小  
>3. 库文件比较小，小于100K，编译时间低，而且可以避免65K方法限制
>4. 支持数据库加密 
>5. 简洁易用的API

[官网地址](http://greenrobot.org/greendao/)  
[Github地址](https://github.com/greenrobot/greenDAO)


### 配置依赖
在项目根目录下的`build.gradle`进行如下配置
```
// In your root build.gradle file:
buildscript {
    repositories {
        jcenter()
        mavenCentral() // add repository
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.3'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
    }
}
```

在需要用到GreenDao的模块下的`build.gradle`进行如下配置。
```
// In your app projects build.gradle file:
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin 
android {
    ··· //其他配置
    
    // add config
    greendao {
        schemaVersion 1
        daoPackage 'com.xx.xx.xx.model.greendao'//随后代码会生成在这个包下
        targetGenDir 'src/main/java'
    }
}
dependencies {
    compile 'org.greenrobot:greendao:3.2.2' // add library
}
```
以上配置完成。

### 定义实体，以及自动生成代码
创建一个实体类
```
package com.reizx.zues.rium.bean;

import org.greenrobot.greendao.annotation.Entity;
import org.greenrobot.greendao.annotation.Id;
import org.greenrobot.greendao.annotation.Generated;

/**
 * Created by kig on 2018/1/9.
 */
@Entity
public class TestEntry {
    @Id(autoincrement = true)
    private Long id;
    private String name;
    private String age;
    private String sex;
    private String salary;
}
```
**点击运行程序，运行完成后会自己在指定包下生成类。**
Get&Set方法都不用自己写，生成代码的时候会自动生成。 

### 具体使用

在`Application`类中初始化数据库相关信息
```
import android.app.Application;
import android.database.sqlite.SQLiteDatabase;

import com.xx.xx.model.greendao.DaoMaster;
import com.xx.xx.model.greendao.DaoSession;

/**
 * Created by kig on 2018/1/9.
 */

public class ReizxApp extends Application {
    private static ZuesRiumApp zuesRiumApp;

    //数据库相关信息
    private DaoMaster.DevOpenHelper devOpenHelper;
    private SQLiteDatabase db;
    private DaoMaster daoMaster;
    private DaoSession daoSession;

    @Override
    public void onCreate() {
        super.onCreate();
        zuesRiumApp = this;
        initDao();
    }

    //初始化数据库，给相关对象赋值
    public void initDao(){
        devOpenHelper = new DaoMaster.DevOpenHelper(this, "xiumdao", null);
        db = devOpenHelper.getWritableDatabase();
        daoMaster = new DaoMaster(db);
        daoSession = daoMaster.newSession();
    }

    public static ZuesRiumApp getZuesRiumApp() {
        return zuesRiumApp;
    }

    public DaoMaster.DevOpenHelper getDevOpenHelper() {
        return devOpenHelper;
    }

    public void setDevOpenHelper(DaoMaster.DevOpenHelper devOpenHelper) {
        this.devOpenHelper = devOpenHelper;
    }

    public SQLiteDatabase getDb() {
        return db;
    }

    public void setDb(SQLiteDatabase db) {
        this.db = db;
    }

    public DaoMaster getDaoMaster() {
        return daoMaster;
    }

    public void setDaoMaster(DaoMaster daoMaster) {
        this.daoMaster = daoMaster;
    }

    public DaoSession getDaoSession() {
        return daoSession;
    }

    public void setDaoSession(DaoSession daoSession) {
        this.daoSession = daoSession;
    }
}

```

代码其他位置中使用：
```
    @OnClick(R.id.btn_app_test)
    public void testNewProcess() {
        Log.d("zues-rium", "test new process");
        TestEntryDao dao = ZuesRiumApp.getZuesRiumApp().getDaoSession().getTestEntryDao();
        Query query = dao.queryBuilder().where(TestEntryDao.Properties.Name.eq("x")).build();
        List<TestEntry> entries = query.list(); //查
        TestEntry testEntry = new TestEntry(null, "x", "16", "女", "xxxx");
        dao.insert(testEntry);//增
        List<TestEntry> entries2 = query.list();
        TestEntry testEntryMod = entries2.get(0);
        testEntry.setSex("男");
        dao.insertOrReplace(testEntry);//改
        DeleteQuery queryDelete = dao.queryBuilder().where(TestEntryDao.Properties.Name.eq("x")).buildDelete();
        queryDelete.executeDeleteWithoutDetachingEntities();//删
    }
```