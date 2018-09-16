---
title: Bean Scope
date: 2018-07-30 9:11:58
tags:
categories:
- Spring
- SpringFramework
- Bean Scope
---
## Introduction
<bean id="role" class="com.kincolle.Role" scope="singleton"/>
Here the scope is used for setting the scope of spring bean. Before spring 2.0 there are 2 kinds of the scope:singleton and prototype. After spring 2.0 session, request and global session has been added. Also you can comtomize your own scope.   


### 1. singleton
It is the default scope and only one shared bean will be in the spring IoC container.

here is an example:

	<bean id="role" class="com.kincolle.Role" scope="singleton"/> 

or

	<bean id="role" class="com.kincolle.Role" singleton="true"/>

### 2. prototype
When a kind of bean has been setted as prototype, every request will create a new bean. Which means after the beans have been created, the container will not be reponsible for it life.

here is an example:

	<bean id="role" class="com.kincolle.Role" scope="prototype"/>

or

	<beanid="role" class="com.kincolle.Role" singleton="false"/>


#### 3. request
When a kind of bean has been setted as request, every http request will create a new bean and it is usefull just in the current HTTP request. When you use request, session or global session, you should have a web.xml like the following(version of servlet is over 2.4):

	<web-app>
	   ...
	  <listener>
	<listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
	  </listener>
	   ...
	</web-app>
 
Then we can set the scope:

	<bean id="role" class="com.kincolle.Role" scope="request"/>

### 4. session
When a kind of bean has been setted as session, every http request will create a new bean and  it is usefull just in the current HTTP session.  Here is how to set it:

	<bean id="role" class="com.kincolle.Role" scope="session"/>

### 5. global session
It is near to session but it is based on web app of portlet．Here is how to set it:

	<bean id="role" class="com.kincolle.Role" scope="global session"/>

