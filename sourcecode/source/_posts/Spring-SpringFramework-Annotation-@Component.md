---
title: @Component
date: 2018-08-05 09:23:03
tags:
categories:
- Spring
- Annotation
- @Component
---
## Type
A class with the @component annotation will be created as a bean into a container. And there are other 3 types:

	1. @controller
	2. @service
	3. @repository
	4. @component

In fact, the @service and @repository is @component. Let's see the sourcecode 

	@Target({ElementType.TYPE})
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	@Component
	public @interface Repository {
		String value() default "";
	}


The following show you how to scan @component class:

	<context:component-scan base-package="com.kincolle">

 
## Name and Scope

You can set beanname and its scope like the following:

	@Service(“beanName”)
	@Scope(“prototype”)
	public class User {
	} 

