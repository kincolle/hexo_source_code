---
title: Generator
date: 2018-06-30 9:10:55
tags:
categories:
- Mybatis
- Generator
---

## Introduction
Here is the Introduction from [mybatis](http://www.mybatis.org/generator/index.html)
 
MyBatis Generator (MBG) is a code generator for MyBatis MyBatis and iBATIS. It will generate code for all versions of MyBatis, and versions of iBATIS after version 2.2.0. It will introspect a database table (or many tables) and will generate artifacts that can be used to access the table(s). This lessens the initial nuisance of setting up objects and configuration files to interact with database tables. MBG seeks to make a major impact on the large percentage of database operations that are simple CRUD (Create, Retrieve, Update, Delete). You will still need to hand code SQL and objects for join queries, or stored procedures.

## Example
Create table kincolle in MySQL

	CREATE TABLE `test`.`kincolle` (
		`id` INT NOT NULL AUTO_INCREMENT,
	  	`username` VARCHAR(40) NOT NULL,
	  	`password` VARCHAR(40) NOT NULL,
	 	 PRIMARY KEY (`id`))
	DEFAULT CHARACTER SET = utf8;


Set database connection,create resources/mybatis/db.properties

	jdbc.driverClassName=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql://127.0.0.1:3306/test
	jdbc.username=root
	jdbc.password=123456

Config mybatis generator,create resources/mybatis/generatorConfig.xml

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE generatorConfiguration
	        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
	        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
	
	<generatorConfiguration>
	
	    <!-- get config file -->
	    <properties resource="mybatis/db.properties"/>
	
	    <context id="mysql" defaultModelType="flat">
	
	        <commentGenerator>
	            <property name="suppressAllComments"  value="true"/>
	        </commentGenerator>
	
	        <!-- jdbc connection -->
	        <jdbcConnection driverClass="${jdbc.driverClassName}"
	                        connectionURL="${jdbc.url}"
	                        userId="${jdbc.username}" password="${jdbc.password}"/>
	
	        <!-- generate entity -->
	        <javaModelGenerator targetPackage="com.learn.test.server.model"
	                            targetProject="src/main/java"/>
	
	        <!-- generate maper.xml file -->
	        <sqlMapGenerator targetPackage="mybatis/mapper"
	                         targetProject="src/main/resources"/>
	
	        <!-- generate dao interface -->
	        <javaClientGenerator targetPackage="com.learn.test.server.dao"
	                             targetProject="src/main/java" type="XMLMAPPER"/>
	
	        <table schema="" tableName="kincolle" domainObjectName="UserDO"
	               enableCountByExample="false" enableDeleteByExample="false"
	               enableSelectByExample="false" enableUpdateByExample="false"/>
	    </context>
	</generatorConfiguration>

Do "mvn mybatis-generator:generate"，UserDOMapper.java,UserDO.java and UserDOMapper.xml will be created.