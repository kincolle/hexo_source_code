---
title: Inner Bean
date: 2018-08-04 14:11:48
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Inner Bean
---

In spring framework, a bean just belong to a specail reference, then it will be an inner bean. The inner bean can do setter and constructor-arg.

Here is an example  

Customer.java


	public class Customer {	 
		private Person person;
		
		public Customer() {
	 
		}
		public Customer(Person person) {
			this.person = person;
		}
		public void setPerson(Person person) {
			this.person = person;
		}
		@Override
		public String toString() {
			return "Customer [person=" + person + "]";
		}
	}

Person.java


	public class Person {
	 
		private String name;
		private String address;
		private int age;
		
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public String getAddress() {
			return address;
		}
		public void setAddress(String address) {
			this.address = address;
		}
		public int getAge() {
			return age;
		}
		public void setAge(int age) {
			this.age = age;
		}
		
		@Override
		public String toString() {
			return "Person [name=" + name + ", address=" + address + ", age=" + age + "]";
		}
	}

At the most time, we can use ref to set Person bean into Customer Bean. 

beans.xml
	
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	 
		<bean id="CustomerBean" class="com.kincolle.Customer">
			<property name="person" ref="PersonBean"/>
		</bean>
		
		<bean id="PersonBean" class="com.kincolle.Person">
			<property name="name" value="Ray"/>
			<property name="address" value="Shanghai"/>
			<property name="Kincolle" value="29"/>
		</bean>
	</beans>

Also, because the Kincolle person bean only is used by Customer bean, so the Kincolle person can be an inner bean:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	 
		<bean id="CustomerBean" class="com.kincolle.Customer">
			<property name="person">
				<bean class="com.kincolle.Person">
					<property name="name" value="Kincolle"/>
					<property name="address" value="Shanghai"/>
					<property name="age" value="29"/>
				</bean>		
			</property>
		</bean>
	</beans>

Inner bean also supports constructor-arg
	
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	 
		<bean id="CustomerBean" class="com.kincolle.Customer">
			<constructor-arg>
				<bean class="com.kincolle.Person">
					<property name="name" value="Kincolle"/>
					<property name="address" value="Shanghai"/>
					<property name="age" value="29"/>
				</bean>		
			</constructor-arg>
		</bean>
	</beans>

Test.java

	public class Test {	 
		public static void main(String[] args) {
			
			ApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");
			Customer test =  (Customer) ctx.getBean("CustomerBean");
			System.out.println(test);
		}
	}

The result will be


	Customer [person=Person [name=Kincolle, address=Shanghai, age=29]]