---
title: mybatis-generator插件的使用
date: 2019-08-26 14:26:47
categories: mybatis
tags: mybatis
---

# Mybatis-generator使用

## **在pom.xml中引入插件**

```xml
<!-- mybatis-generator -->
<plugin>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-maven-plugin</artifactId>
	<version>1.3.7</version>
	<configuration>
		<verbose>true</verbose>
		<overwrite>true</overwrite>
	</configuration>
	<!--
		  用maven mybatis插件
		  如果不在plugin里面添加依赖包得引用的话，会找不到相关得jar包，
		  在plugin外部得jar包，他不会去找到并执行，
		  所以要把plugin运行依赖得jar配置都放在里面
		  -->
	<dependencies>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.2.8</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>${mysql.version}</version>
		</dependency>
	</dependencies>
</plugin>
```

在resources目录下添加generatorConfig.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>

    <!-- context 是逆向工程的主要配置信息 -->
    <!-- id：起个名字 -->
    <!-- targetRuntime：设置生成的文件适用于那个 mybatis 版本 -->
    <context id="default" targetRuntime="MyBatis3">


        <!-- optional，旨在创建class时，对注释进行控制 -->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--jdbc的数据库连接-->
        <!--这里是以变量的形式定义，具体的值在 application.properties 文件中-->
        <!--jdbc:mysql://localhost:3306/数据库名?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC-->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC"
                        userId="root"
                        password="admin">
            <!--MySQL 不支持 schema 或者 catalog 所以需要添加这个-->
            <!--参考 : http://www.mybatis.org/generator/usage/mysql.html-->
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>

        <!-- targetPackage：生成的实体类所在的包 -->
        <!-- targetProject：生成的实体类所在的硬盘位置 -->
        <!-- DataObject 类存放位置 -->
        <javaModelGenerator targetPackage="com.example.model"
                            targetProject=".\src\main\java">
            <!-- 是否允许子包 -->
            <property name="enableSubPackages" value="false" />
            <!-- 是否对modal添加构造函数 -->
            <property name="constructorBased" value="true" />
            <!-- 是否清理从数据库中查询出的字符串左右两边的空白字符 -->
            <property name="trimStrings" value="true" />
            <!-- 建立modal对象是否不可改变 即生成的modal对象不会有setter方法，只有构造方法 -->
            <property name="immutable" value="false" />
        </javaModelGenerator>

        <!-- targetPackage 和 targetProject：生成的 mapper 文件的包和位置 -->
        <sqlMapGenerator targetPackage="mapper"
                         targetProject=".\src\main\resources">
            <!-- 针对数据库的一个配置，是否把 schema 作为字包名 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>

        <!-- targetPackage 和 targetProject：生成的 interface 文件的包和位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.example.mapper" targetProject=".\src\main\java">
            <!-- 针对 oracle 数据库的一个配置，是否把 schema 作为字包名 -->
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>

        <!--生成对应表及类名 并不生成Example-->
        <table tableName="user" domainObjectName="User" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"/>
        <table tableName="user_company" domainObjectName="UserCompany" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false" />
        <table tableName="pub_parameter" domainObjectName="PubParameter" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false"/>
        <table tableName="funds_record" domainObjectName="FundsRecord" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false"/>
        <table tableName="company_extension" domainObjectName="CompanyExtension" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false"/>
    </context>
</generatorConfiguration>

```

## **运行插件**

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904143558.png"/>

**最终项目结构**

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904143631.png"/>

## 常见的坑说明

**MyBatis Generator 生成器把其他数据库的同名表生成下来的问题**

​		

```
MyBatis Generator : Table Configuration scheme.table matched more than one table
```

在生成器的配置文件里的数据库连接地址中添加下列参数:

```
nullCatalogMeansCurrent=true
```

```xml
<!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
<jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
				connectionURL="jdbc:mysql://localhost:3306/security"
				userId="root"
				password="root">
	<!--MySQL 8.x 需要指定服务器的时区-->
	<property name="serverTimezone" value="UTC"/>
	<!--MySQL 不支持 schema 或者 catalog 所以需要添加这个-->
	<!--参考 : http://www.mybatis.org/generator/usage/mysql.html-->
	<property name="nullCatalogMeansCurrent" value="true"/>
</jdbcConnection>
```

**Exception getting JDBC Driver: com.mysql.jdbc.Driver**

用maven mybatis插件，如果不在plugin里面添加依赖包得引用的话，会找不到相关得jar包

```xml
<!-- mybatis-generator -->
 <plugin>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-maven-plugin</artifactId>
	<version>1.3.7</version>
	<configuration>
		<verbose>true</verbose>
		<overwrite>true</overwrite>
	</configuration>
	<!--
		  用maven mybatis插件
		  如果不在plugin里面添加依赖包得引用的话，会找不到相关得jar包，
		  在plugin外部得jar包，他不会去找到并执行，
		  所以要把plugin运行依赖得jar配置都放在里面
		  -->
	<dependencies>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.2.8</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>${mysql.version}</version>
		</dependency>
	</dependencies>
</plugin>
```

**mybaties generator 驼峰配置**

修改表格部分如下：

```xml
<table tableName="user_info"
               enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false"
               selectByExampleQueryId="false">
            <property name="useActualColumnNames" value="false" />
            <!--true：MyBatis Generator会使用数据库中实际的字段名字作为生成的实体类的属性名。
        　　false：这是默认值。如果设置为false,则MyBatis Generator会将数据库中实际的字段名字转换为Camel Case风格作为生成的实体类的属性名。-->
        </table>
```

