---
title: Value
date: 2018-08-05 22:01:48
tags:
categories:
- Spring
- SpringFramework
- Annotation
- Value
---
If you have a dev.properties file, and need to inject the following tag:

	tag=123

#### Through PropertyPlaceholderConfigurer
	
	<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
	    <property name="location" value="dev.properties" />
	</bean>

Code

	@Value("${tag}")
	private String tag;


#### Through PreferencesPlaceholderConfigurer

	<bean id="appConfig" class="org.springframework.beans.factory.config.PreferencesPlaceholderConfigurer">
	    <property name="location" value="dev.properties" />
	</bean>

Code
	
	@Value("${tag}")
	private String tag;

#### PropertiesFactoryBean

	<bean id="config" class="org.springframework.beans.factory.config.PropertiesFactoryBean">
        <property name="location" value="dev.properties" />
    </bean>

Code
	
	@Value("#{config['tag']}")
	private String tag;
