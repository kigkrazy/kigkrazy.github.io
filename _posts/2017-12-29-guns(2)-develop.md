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
打开`guns-rest`模块下的单元测试中的一个类`com.stylefeng.guns.generator.EntityGenerator`
#### 配置生成信息
##### 手动配置生成
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

##### 框架模块生成
打开模块`guns-generator`中的`com.stylefeng.guns.generator.action.GunsCodeGenerator`。

...此处防空，后面填坑。

### 生成基本的业务代码


### 配置菜单和角色

### 进一步开发业务


**注意:** 
* Guns需要jdk1.8环境
* MySql密码不能设置为全部是‘0’（例如：000000），这样的代码在初始化数据库的时候传入的密码变成：“0”