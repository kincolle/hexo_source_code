---
title: The IoC Container
date: 2018-07-27 23:21:28
tags:
categories:
- Spring
- SpringFramework
- The IoC Container
---
## Introduction
One of the most important part in SpringFramework is IoC. Here let's see what it is.

## 1. The IoC container
## 1.1. Introduction to the Spring IoC container and beans
本章介绍Spring Framework实现控制反转（IoC）[1]原理。 IoC也被称为依赖注入（DI）。 它是一个过程，对象通过构造函数参数，工厂方法的参数或在工厂方法构造或返回后在对象实例上设置的属性来定义它们的依赖关系，即它们使用的其他对象。 容器在创建bean时会注入这些依赖关系。 这个过程从根本上来说是相反的，因此名为控制反转（IoC），bean本身通过使用类的直接构造或诸如Service Locator模式之类的机制来控制其依赖关系的实例化或位置。

org.springframework.beans和org.springframework.context包是Spring Framework的IoC容器的基础。 BeanFactory接口提供了一种能够管理任何类型对象的高级配置机制。 ApplicationContext是BeanFactory的一个子接口。 它增加了与Spring的AOP功能更容易的集成; 消息资源处理（用于国际化），事件发布; 以及Web应用程序上下文（如Web应用程序上下文）以用于Web应用程序。

简而言之，BeanFactory提供了配置框架和基本功能，而ApplicationContext添加了更多的企业特定功能。 ApplicationContext是BeanFactory的一个完整的超集，在本章中专门用于描述Spring的IoC容器。 有关使用BeanFactory而不是ApplicationContext的更多信息，请参阅BeanFactory。

在Spring中，构成应用程序主干和由Spring IoC容器管理的对象称为bean。 bean是一个实例化，组装并由Spring IoC容器管理的对象。 否则，bean只是应用程序中众多对象中的一个。 Bean和它们之间的依赖关系反映在容器使用的配置元数据中。

1.2. Container overview
接口org.springframework.context.ApplicationContext表示Spring IoC容器，并负责实例化，配置和组装上述bean。 容器通过读取配置元数据获取有关要实例化，配置和组装的对象的指示信息。 配置元数据用XML、Java注释或Java代码表示。 它允许您表示组成应用程序的对象以及这些对象之间丰富的相互依赖关系。

Spring提供了几个ApplicationContext接口的实现。 在独立应用程序中，通常会创建ClassPathXmlApplicationContext或FileSystemXmlApplicationContext的实例。 虽然XML是用于定义配置元数据的传统格式，但您可以通过提供少量的XML配置来指示容器使用Java注释或代码作为元数据格式，以声明方式支持这些其他元数据格式。

在大多数应用场景中，显式用户代码不需要实例化Spring IoC容器的一个或多个实例。 例如，在Web应用程序场景中，应用程序的web.xml文件中简单的8个（或多个）样板Web描述符XML行就足够了（请参阅Web应用程序的便捷ApplicationContext实例化）。 如果您使用的是Spring工具套件Eclipse驱动的开发环境，则只需点击几下鼠标或按键即可轻松创建此样板配置。

下图是Spring如何工作的高级视图。 您的应用程序类与配置元数据相结合，以便在创建并初始化ApplicationContext之后，您拥有完全配置且可执行的系统或应用程序。