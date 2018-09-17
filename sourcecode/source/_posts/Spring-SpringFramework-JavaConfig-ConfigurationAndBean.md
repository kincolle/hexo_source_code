---
title: Configuration And Bean
date: 2018-08-09 23:01:05
tags:
categories:
- Spring
- SpringFramework
- JavaConfig
- Configuration And Bean
---
## Introduction
In spring, besides XML config, we also can use java config to do our spring.

Here is an example of XML and Java Config which is the same:


## XML config

	<beans>
		<bean id="course" class="demo.Course">
			<property name="module" ref="module"/>
	  	</bean>
		
		<bean id="module" class="demo.Module">
			<property name="assignment" ref="assignment"/>
	  	</bean>
		
		<bean id="assignment" class="demo.Assignment" />
	</beans>
			

##

	@Configuration
	public class AppContext {
		@Bean
		public Course course() {
			Course course = new Course();
			course.setModule(module());
			return course;
		}
	
		@Bean
		public Module module() {
			Module module = new Module();
			module.setAssignment(assignment());
			return module;
		}
	
		@Bean
		public Assignment assignment() {
			return new Assignment();
		}
	}