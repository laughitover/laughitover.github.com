---
layout: post
title: 使用mybatis根据表结构自动创建代码
category: practice
---
## 一.背景
MyBatis属于一种半自动的ORM（object relation mapping）对象关系映射框架，可以利用MyBatis生成器自动生成实体类、DAO接口和Mapping映射文件。将生成的代码copy到项目工程中，可以节省很多开发时间。

使用自动生成有很多方式，可以在eclipse中安装插件，但是以下将要介绍的是最简单的方式，只需要下几个jar包即可，把它们放在一个目录下面。

## 二.下载jar包
jar包下载地址:[mybatis-generator](https://github.com/laughitover/mybatis-generator)

生成代码需要的文件和jar包：
![目录结构](http://www.laughitover.com/assets/images/2018/0528-01.png)

## 三.修改配置文件
其中有mybatis框架的jar包，数据库驱动程序jar包以及MyBatis生成器jar包。其中的generatorConfig.xml是需要我们来配置的文件，配置如下：

```
<span style="font-size:18px;"><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
<!-- 数据库驱动-->
    <classPathEntry  location="mysql-connector-java-5.1.25-bin.jar"/>
    <context id="DB2Tables"  targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库链接URL，用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
		connectionURL="jdbc:mysql://127.0.0.1/test" userId="root" password="123456">
        </jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="test1.domain" targetProject="src">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="test1.mapping" targetProject="src">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="test1.IDao" targetProject="src">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->
        <table tableName="user_info" domainObjectName="User" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
    </context>
</generatorConfiguration>  </span> 
```

## 四.生成代码
打开控制台，进入lib目录下，执行脚本：
java -jar mybatis-generator-core-1.3.2.jar -configfile generatorConfig.xml -overwrite 

成功之后，就可以在src目录下找到相应的文件夹，每个表都会对应三个文件（实体类、接口、配置文件）。
