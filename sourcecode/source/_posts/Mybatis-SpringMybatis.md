---
title: Spring Mybatis
date: 2018-07-05 22:55:18
tags:
categories:
- Mybatis
- Spring Mybatis
---

## Introduction
This article gives an example of usage of Spring Mybatis.

##### 1. Build table

	create database studentDB;
	use studentDB;
	create table student(id char(20) primary key,name varchar(40),age tinyint,sex char(1));
	insert into student values('123','kincolle',22,'male');
	insert into student values('456','jin',21,female');

###### 2. Difine bean and dao and xml

bean（student.java）

	@Component
	public class Student {
	    private String id;
	    private String name;
	    private byte age;
	    private String sex;
	    public String getId() {
	        return id;
	    }
	    public void setId(String id) {
	        this.id = id;
	    }
	    public String getName() {
	        return name;
	    }
	    public void setName(String name) {
	        this.name = name;
	    }
	    public int getAge() {
	        return age;
	    }
	    public void setAge(byte age) {
	        this.age = age;
	    }
	    public String getSex() {
	        return sex;
	    }
	    public void setSex(String sex) {
	        this.sex = sex;
	    }
	
	}


dao（studentDao.java）

	public interface StudentDao {
	    public int updateStudent(Student s);
	    public List<Student> getAllStudent();
	}

XML（studentDao.xml）

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"   
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd"> 
	<mapper namespace="com.kincolle.dao.StudentDao">
	    <update id="updateStudent" parameterType="com.kincolle.beans.Student">
	        update student set name=#{name},age=#{age},sex=#{sex} where id=#{id}
	    </update>
	    <resultMap type="com.kincolle.beans.Student" id="students">
	        <id column="id" property="id" jdbcType="CHAR"/>
	        <result column="name" property="name" jdbcType="VARCHAR"/>
	        <result column="age" property="age" jdbcType="TINYINT" />
	        <result column="sex" property="sex" jdbcType="CHAR"/>
	    </resultMap>
	    <select id="getAllStudent" resultMap="students">
	        select * from student
	    </select>
	</mapper>

mybatis.xml
	
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd"> 
	<configuration>
	    <mappers>
	        <mapper resource="com/kincolle/dao/StudentDao.xml"/>
	    </mappers>
	</configuration>


##### 2. spring.xml
######  config mysql datasource

	<bean id="dataSource"                   class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
	        <property name="url" value="jdbc:mysql://localhost:3306/newsDB?characterEncoding=UTF-8"></property>
	        <property name="username" value="root"></property>
	        <property name="password" value="123456"></property>
	</bean>


###### config sqlsessionfactory

	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	            <property name="configLocation" value="classpath:MyBatis-Configuration.xml"></property>
	            <property name="dataSource" ref="dataSource" />
	</bean>

###### config studentDao

	<bean id="studentDao" class="org.mybatis.spring.mapper.MapperFactoryBean">  
        <property name="mapperInterface" value="com.kincolle.dao.StudentDao"></property>  
        <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>  
    </bean> 

##### 5. test

	public class Main {
	    public static void main(String[] args) {
	        ApplicationContext atc = new ClassPathXmlApplicationContext("spring.xml");
	        Student s = (Student) atc.getBean("student");
	        s.setId("123");
	        s.setName("kincolle");
	        s.setAge((byte)22);
	        s.setSex("male");
	
	        StudentDao dao = (StudentDao) atc.getBean("studentDao");
	        System.out.println(dao.updateStudent(s));
	    }
	
	}

the result is 1.

	public class Main {
	    public static void main(String[] args) {
	        ApplicationContext atc = new ClassPathXmlApplicationContext("spring.xml");      
	        StudentDao dao = (StudentDao) atc.getBean("studentDao");
	        List<Student> students = dao.getAllStudent();
	        System.out.println(students.size());
	    }
	}

the result is 2.