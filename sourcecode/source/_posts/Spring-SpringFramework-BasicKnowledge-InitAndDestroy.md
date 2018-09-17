---
title: Init and Destroy
date: 2018-08-07 22:41:31
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Init and Destroy
---
## Introduction
The init-method and destroy-method of spring can do things before or after invoke function. Here I will show you an example. 

#### define an interface

	public interface StudentService {
	    public void helloSpring(String str);
	}

#### StudentService implementation


	public class StudentServiceImpl implements StudentService,Serializable{	
	    private static final long serialVersionUID = 6130145558179499205L;
	 
	    @Override
	    public void helloSpring(String str) {
	        // TODO Auto-generated method stub
	        System.err.println("run  this is "+str);
	    }
	    
	    public void inits(){
	        System.err.println("do before run helloSpring");
	    }
	
	    public void shutdown(){
	        System.err.println("destroy studentService instence before invoke shutdown() function");
	    }
	}

#### XML file
	
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
	    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:context="http://www.springframework.org/schema/context"
	    xsi:schemaLocation="  
	         http://www.springframework.org/schema/context   
	         http://www.springframework.org/schema/context/spring-context-3.0.xsd    
	     http://www.springframework.org/schema/beans   
	     http://www.springframework.org/schema/beans/spring-beans-3.0.xsd  
	     http://www.springframework.org/schema/tx   
	     http://www.springframework.org/schema/tx/spring-tx-3.0.xsd  
	     http://www.springframework.org/schema/aop   
	     http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
	        
	        
	    <bean id="stu" class="com.kincolle.StudentServiceImpl" init-method="inits" destroy-method="shutdown"></bean>
	
	</beans>

#### Test

	public class TestDemo implements Serializable {
	
	    private static final long serialVersionUID = 6343872716391435079L;
	    
	    public static void main(String[] args){
	        
	        
	        ApplicationContext context = new ClassPathXmlApplicationContext(new String[]{"applicationContext.xml"});
	        
	        StudentService studentService = context.getBean("stu", StudentService.class);
	        
	        studentService.helloSpring("Hello! Spring!");
	        
	        ((ClassPathXmlApplicationContext) context).close();  
	        
	    }
	}

