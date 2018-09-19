---
title: BeanFactory And ApplicationContext
date: 2018-08-12 19:37:21
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- BeanFactory And ApplicationContext
---
  
## Relationship of Main Interfaces in Spring

![](Spring-SpringFramework-BasicKnowledge-BeanFactoryAndApplicationContext/1.png)

## BeanFactory Design Route
BeanFactory -> HierarchicalBeanFactory -> ConfigurableBeanFactory is the main BeanFactory design route.

- BeanFactory: Basic rules like getBean() function
- HierarchicalBeanFactory: Manage family IoC container like getParentBeanFactory() function
- ConfigurableBeanFactory: config of BeanFactory like using setParentBeanFactory() to set family IoC container

## ApplicationContext Design Route
BeanFactory -> ListableBeanFactory and HierarchicalBeanFactory -> ApplicationContext -> ConfigurableApplicationContext is the main ApplicationContext design route。

ListableBeanFactory: Let function of BeanFactory be more usefull like getBeanDefinitionNames()
ApplicationContext: Implements MessageSource, ResourceLoader and ApplicationEventPublisher interface to add more functions