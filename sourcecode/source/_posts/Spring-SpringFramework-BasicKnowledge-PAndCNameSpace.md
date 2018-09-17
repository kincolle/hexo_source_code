---
title: P And C Name Space
date: 2018-08-01 21:51:34
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- P And C Name Space
---
## P Name Space
The P Name Space is used for replacing <property> element. 
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns:p="http://www.springframework.org/schema/p"
	    xsi:schemaLocation="http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <!-- stardard XML format -->
	    <bean name="john-classic" class="com.knicolle.Person">
	        <property name="name" value="John Doe"/>
	        <property name="spouse" ref="jane"/>
	    </bean>
	
	    <!-- p name space format -->
	    <bean name="john-modern"
	        class="com.knicolle.Person"
	        p:name="John Doe"
	        p:spouse-ref="jane"/>
	
	</beans>

## C Name Space
The P Name Space is used for replacing <constructor-arg> element. 
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns:c="http://www.springframework.org/schema/c"
	    xsi:schemaLocation="http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <bean id="bar" class="x.y.Bar"/>
	    <bean id="baz" class="x.y.Baz"/>
	
	    <!-- stardard XML format -->
	    <bean id="foo" class="x.y.Foo">
	        <constructor-arg name="bar" ref="bar"/>
	        <constructor-arg name="baz" ref="baz"/>
	        <constructor-arg name="email" value="foo@bar.com"/>
	    </bean>
	
	    <!-- c name space format -->
	    <bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/>
	
		<!-- param index of c name space format -->
	    <bean id="foo" class="x.y.Foo" c:_0-ref="bar" c:_1-ref="baz" c:_2="foo@bar.com"/>
	
	</beans>