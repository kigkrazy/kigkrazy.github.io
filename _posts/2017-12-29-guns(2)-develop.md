---
title: 基于GUNS的二次开发
categories:
 - guns
 - 后端
tags:
 - guns
 - 后端
---

本文将以一个`订单业务(增删改查)`为例子，向大家展示GUNS的后台开发的简单粗暴。

### 准备数据库表
在SQL中执行语句：
```
CREATE TABLE `my_order` (
	`id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键id',
	`user` VARCHAR(255) DEFAULT NULL COMMENT '下单人',
	`place` VARCHAR(255) DEFAULT NULL COMMENT '地点',
	`goods` VARCHAR(255) DEFAULT NULL COMMENT '商品名',
	`ordertime` DATETIME DEFAULT NULL COMMENT '下单时间',
	PRIMARY KEY(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
创建一张名为my_order的表。

### 自动生成表对应的实体和数据库操作DAO
#### 手动配置mybatis-plus生成
打开`guns-rest`模块下的单元测试中的一个类`com.stylefeng.guns.generator.EntityGenerator`
```
    public void entityGenerator() {
        AutoGenerator mpg = new AutoGenerator();
        String workDir = System.getProperty("user.dir");
        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        // 步骤一、将gc.setOutputDir中的参数改为${项目路径}\\guns-admin\\src\\main\\java
        gc.setOutputDir(workDir + "\\..\\guns-admin\\src\\main\\java");//这里写你自己的java目录
        gc.setFileOverride(true);//是否覆盖
        gc.setActiveRecord(true);
        gc.setEnableCache(false);// XML 二级缓存
        gc.setBaseResultMap(true);// XML ResultMap
        gc.setBaseColumnList(false);// XML columList
        gc.setAuthor("stylefeng");
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setDbType(DbType.MYSQL);
        dsc.setTypeConvert(new MySqlTypeConvert() {
            // 自定义数据库表字段类型转换【可选】
            @Override
            public DbColumnType processTypeConvert(String fieldType) {
                return super.processTypeConvert(fieldType);
            }
        });
        dsc.setDriverName("com.mysql.jdbc.Driver");
        // 步骤二、修改数据库连接，以及用户名密码
        dsc.setUsername("root");
        dsc.setPassword("123456");
        dsc.setUrl("jdbc:mysql://127.0.0.1:3306/rest?characterEncoding=utf8");
        mpg.setDataSource(dsc);

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        //strategy.setTablePrefix(new String[]{"_"});// 此处可以修改为您的表前缀
        strategy.setNaming(NamingStrategy.underline_to_camel);// 表名生成策略
        //步骤三、此处添加要生成模板的表名。此处一定要添加，否则将生成所有的表的模板，并且把旧的覆盖。
        strategy.setInclude(new String[]{"my_order"});
        mpg.setStrategy(strategy);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setParent(null);
        // 步骤三、配置要生成的包的位置
        // 一般改为以下包名路径：
        //pc.setEntity("com.stylefeng.guns.common.persistence.model");
        //pc.setMapper("com.stylefeng.guns.common.persistence.dao");
        //pc.setXml("com.stylefeng.guns.common.persistence.dao.mapping");
        pc.setEntity("com.stylefeng.guns.rest.persistence.model");
        pc.setMapper("com.stylefeng.guns.rest.persistence.dao");
        pc.setXml("com.stylefeng.guns.rest.persistence.dao.mapping");
        pc.setService("TTT");       //本项目没用，生成之后删掉
        pc.setServiceImpl("TTT");   //本项目没用，生成之后删掉
        pc.setController("TTT");    //本项目没用，生成之后删掉
        mpg.setPackageInfo(pc);

        // 注入自定义配置，可以在 VM 中使用 cfg.abc 设置的值
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                Map<String, Object> map = new HashMap<>();
                map.put("abc", this.getConfig().getGlobalConfig().getAuthor() + "-mp");
                this.setMap(map);
            }
        };
        mpg.setCfg(cfg);

        // 执行生成
        mpg.execute();

        // 打印注入设置
        System.err.println(mpg.getCfg().getMap().get("abc"));
    }
```
生成成功后，会自动弹出文件夹，此时将`TTT`文件夹删掉即可。


#### 封装调用
1. 在模块`guns-generator`中的`com.stylefeng.guns.generator.action`建一个类`RzGunsMpGeneration`。  
代码如下：
```
package com.stylefeng.guns.generator.action;

import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.DbType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.stylefeng.guns.core.util.FileUtil;
import sun.rmi.runtime.Log;

import java.io.File;

/**
 * 符合模块二次开发的Mybatis-Plus的代码生成器封装
 *
 * 一般使用方法:
 * RzGunsMpGeneration rzGunsMpGeneration = new RzGunsMpGeneration("reizx");
 * rzGunsMpGeneration.setMpDataSource("127.0.0.1", 3306, "root", "123456", "guns");
 * rzGunsMpGeneration.setMpStrategyConfig(new String[]{"Order"});
 * rzGunsMpGeneration.execute();
 */
public class RzGunsMpGeneration {
    private final String jdbcUrl =  "jdbc:mysql://${host}:${port}/${dbName}?characterEncoding=utf8";
    AutoGenerator autoGenerator = new AutoGenerator();
    GlobalConfig globalConfig = new GlobalConfig();
    DataSourceConfig dataSourceConfig = new DataSourceConfig();
    PackageConfig packageConfig = new PackageConfig();
    StrategyConfig strategyConfig = new StrategyConfig();
    String[] tables; //哪些表需要生成代码

    public RzGunsMpGeneration(){
        constructInit("reizx");
    }

    public RzGunsMpGeneration(String author){
        constructInit(author);
    }

    /**
     * 构造函数初始化
     * @param author
     */
    public void constructInit(String author){
        setMpGlobalConfig(author);
        setMpPackageConfig();
    }

    /**
     * 设置全局配置
     * @param author 用户名
     */
    public void setMpGlobalConfig(String author){
        String projectDir = System.getProperty("user.dir");
        String srcDir = projectDir + "\\..\\guns-admin\\src\\main\\java";
        globalConfig.setOutputDir(srcDir);//写自己项目的绝对路径,注意具体到java目录
        globalConfig.setFileOverride(true);// 是否覆盖
        globalConfig.setEnableCache(false);// XML 二级缓存
        globalConfig.setBaseResultMap(true);// XML ResultMap
        globalConfig.setBaseColumnList(true);// XML columList
        globalConfig.setOpen(false);//是否打开输出目录
        globalConfig.setAuthor(author);
    }

    /**
     * 设置数据库源
     * @param host 数据库IP
     * @param port 数据库端口
     * @param user 用户名
     * @param password 密码
     * @param dbName 数据库名称
     * @return
     */
    RzGunsMpGeneration setMpDataSource(String host, int port,
                                          String user, String password,
                                          String dbName) {
        String connectUrl = jdbcUrl.replace("${host}", host)
                .replace("${port}", "" + port)
                .replace("${dbName}", dbName);
        dataSourceConfig.setDbType(DbType.MYSQL);
        dataSourceConfig.setDriverName("com.mysql.jdbc.Driver");
        dataSourceConfig.setUsername(user);
        dataSourceConfig.setPassword(password);
        dataSourceConfig.setUrl(connectUrl);
        return this;
    }

    public RzGunsMpGeneration setMpStrategyConfig(String[] tables){
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setInclude(tables);
        return this;
    }

    /**
     * 设置包信息，此处写死，需要扩展的话可以扩展
     */
    protected void setMpPackageConfig() {
        packageConfig.setParent(null);
        packageConfig.setEntity("com.stylefeng.guns.common.persistence.model");
        packageConfig.setMapper("com.stylefeng.guns.common.persistence.dao");
        packageConfig.setXml("com.stylefeng.guns.common.persistence.dao.mapping");
        packageConfig.setService("TTT");       //本项目没用，生成之后删掉
        packageConfig.setServiceImpl("TTT");   //本项目没用，生成之后删掉
        packageConfig.setController("TTT");    //本项目没用，生成之后删掉
    }

    /**
     * 生成代码
     */
    public void execute(){
        if (strategyConfig.getInclude() == null || strategyConfig.getInclude().length == 0){
            System.out.println("the strategyConfig->include is null, it won't generate code.");
            return;
        }
        execute(strategyConfig.getInclude());
    }

    /**
     * 生成代码
     * @param tables 需要生成代码的表
     */
    public void execute(String[] tables){
        if (tables == null || tables.length == 0){
            System.out.println("the strategyConfig->include is null, it won't generate code.");
            return;
        }
        strategyConfig.setInclude(tables);
        autoGenerator.setGlobalConfig(globalConfig);
        autoGenerator.setStrategy(strategyConfig);
        autoGenerator.setDataSource(dataSourceConfig);
        autoGenerator.setPackageInfo(packageConfig);
        autoGenerator.execute();
        String outputDir = globalConfig.getOutputDir() + "/TTT";
        FileUtil.deleteDir(new File(outputDir));
    }
}

```
在新建一个执行类`RzGenerate`
```
package com.stylefeng.guns.generator.action;

import com.stylefeng.guns.generator.engine.SimpleTemplateEngine;
import com.stylefeng.guns.generator.engine.base.GunsTemplateEngine;
import com.stylefeng.guns.generator.engine.config.ContextConfig;

public class RzGenerate {
    public static void main(String[] args) {
        genAdiCode();
    }

    public static void genMpCode(){
        RzGunsMpGeneration rzGunsMpGeneration = new RzGunsMpGeneration("reizx");
        rzGunsMpGeneration.setMpDataSource("127.0.0.1", 3306, "root", "123456", "guns");
        rzGunsMpGeneration.setMpStrategyConfig(new String[]{"my_order"});//此处一定要加否则不生成
        rzGunsMpGeneration.execute();
    }
}

```
#### 执行效果
1. 在`com.stylefeng.guns.common.persistence.dao`包下生成`MyOrderMapper.java`。
2. 在`com.stylefeng.guns.common.persistence.dao.mapping`包下生成`MyOrderMapper.xml`。
3. 在`com.stylefeng.guns.common.persistence.model`包下生成`MyOrder.java`。
**一般我们推荐使用这种方法。**

### 生成基本的业务代码
#### 生成方法
在模块`guns-generator`中上一个步骤中生成的`com.stylefeng.guns.generator.action.RzGenerate`中添加`genAdiCode`函数。 整体代码如下： 
```
package com.stylefeng.guns.generator.action;

import com.stylefeng.guns.generator.engine.SimpleTemplateEngine;
import com.stylefeng.guns.generator.engine.base.GunsTemplateEngine;
import com.stylefeng.guns.generator.engine.config.ContextConfig;

public class RzGenerate {
    public static void main(String[] args) {
        genMpCode();//执行Mapper生成
        genAdiCode();//执行业务框架代码生成
    }

    public static void genMpCode(){
        RzGunsMpGeneration rzGunsMpGeneration = new RzGunsMpGeneration("reizx");
        rzGunsMpGeneration.setMpDataSource("127.0.0.1", 3306, "root", "123456", "guns");
        rzGunsMpGeneration.setMpStrategyConfig(new String[]{"my_order"});
        rzGunsMpGeneration.execute();
    }

    public static  void genAdiCode(){
        GunsTemplateEngine gunsTemplateEngine = new SimpleTemplateEngine();
        ContextConfig contextConfig = new ContextConfig();
        contextConfig.setBizChName("订单业务");
        contextConfig.setBizEnName("Order");
        contextConfig.setModuleName("Order");
        gunsTemplateEngine.setContextConfig(contextConfig);
        gunsTemplateEngine.start();//生成业务代码
    }
}
```
#### 执行效果
1. 生成`com.stylefeng.guns.modular.Order.controller.OrderController`
2. 生成`com.stylefeng.guns.common.persistence.model.Order`
3. 生成`com.stylefeng.guns.modular.Order.service.IOrderService`
4. 生成`${工程目录}\guns-parent\..\guns-admin\src\main\webapp\WEB-INF\view\Order\Order\Order.html`
5. 生成`${工程目录}\guns-parent\..\guns-admin\src\main\webapp\WEB-INF\view\Order\Order\Order_add.html`
6. 生成`${工程目录}\guns-parent\..\guns-admin\src\main\webapp\WEB-INF\view\Order\Order\Order.js`
7. 生成`${工程目录}\guns-parent\..\guns-admin\src\main\webapp\WEB-INF\view\Order\Order\Order_info.js`
8. 生成`${工程目录}\guns-parent\..\guns-admin\src\main\java\Order.sql`

### 配置菜单和角色

### 进一步开发业务


**注意:** 
* Guns需要jdk1.8环境
* MySql密码不能设置为全部是‘0’（例如：000000），这样的代码在初始化数据库的时候传入的密码变成：“0”