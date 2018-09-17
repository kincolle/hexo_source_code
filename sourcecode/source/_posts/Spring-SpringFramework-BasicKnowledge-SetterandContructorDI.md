---
title: Setter And Contructor DI
date: 2018-07-29 22:11:08
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Setter And Contructor DI
---
## Introduction
In spring, we always use Setter or Contructor ways to create beans. Here let's see it:

## Setter DI
The bean class is:

    public class Id {  
        private int id;  
        private String name;  
        public int getId() {  
            return id;  
        }  
        public void setId(int id) {  
            this.id = id;  
        }  
        public String getName() {  
            return name;  
        }  
        public void setName(String name) {  
            this.name = name;  
        }  
    }

the Id_Bean.xml file:

    <?xml version="1.0" encoding="UTF-8"?>  
    <beans xmlns="http://www.springframework.org/schema/beans"  
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
           xsi:schemaLocation="http://www.springframework.org/schema/beans  
               http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">  

        <bean id="id" class="com.kincolle.Id">  
        <property name="id" value="1"></property>  
        <property name="name" value="kincolle"></property>  
        </bean>  
    </beans> 

test it:

    public class IdTest {  
        public static void main(String[] args){  
            ClassPathXmlApplicationContext context = new   
                    ClassPathXmlApplicationContext("com/kincolle/Id_Bean.xml");  
            Id id = (Id)context.getBean("id");  
            System.out.println(id.getId());  
            System.out.println(id.getName());  
        }  
    }  

the Result will be
	
	1 kincolle

## Constructor DI
The bean class is:

    public class Id {  
        private int id;  
        private String name;  
		// use constructor to create bean
        public Id(int id,String name){  
            this.id = id;  
            this.name = name;  
        }  
        public int getId() {  
            return id;  
        }  
        public void setId(int id) {  
            this.id = id;  
        }  
        public String getName() {  
            return name;  
        }  
        public void setName(String name) {  
            this.name = name;  
        }  
    }  

the Id_Bean.xml file:

    <?xml version="1.0" encoding="UTF-8"?>  
    <beans xmlns="http://www.springframework.org/schema/beans"  
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
           xsi:schemaLocation="http://www.springframework.org/schema/beans  
               http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">  

        <bean id="id" class="com.kincolle.Id">  
        <constructor-arg index="0" value="1"></constructor-arg>  
        <constructor-arg index="1" value="kincolle"></constructor-arg>  
        </bean>  
    </beans>  

test it:
	
	1 kincolle

