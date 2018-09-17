---
title: Autowired
date: 2018-08-05 10:01:13
tags:
categories:
- Spring
- SpringFramework
- Annotation
- Autowired
---
## Introduction
@Autowired is added into spring since Spring2.5. It can set bean into member variable, function or contructor with this annotation without xml file. The @Autowired works according to bean type, so, if you want to it works with bean name you need to use @Qualifier.

## Example
xml file:

	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">
	    <context:component-scan base-package="kincolle"/>
	    <bean id="cdPlayer" class="kincolle.CDPlayer"/>
	</beans>

An interface

	public interface CompactDisc {
	    void play();
	}

Use @Component to define a bean.
	
	@Component
	public class SgtPeppers implements CompactDisc{
	    @Override
	    public void play() {
	        System.out.println("SgtPeppers playing....");
	    }
	}

Let's see 3 types of using @Autowired:

1. function

	public class CDPlayer {
	    CompactDisc cd;   
	    @Autowired
	    public void setCompactDisc(CompactDisc cd){
	        this.cd = cd;
	    }
	    public void say(){
	        cd.play();
	    }
	}

2. member variable

	public class CDPlayer{
	     @Autowired
	     CompactDisc cd;
	     public void say(){
	          cd.play();
	     }
	}

3. constructor

	public class CDPlayer {
	    CompactDisc cd;   
	    @Autowired
	    public CDPlayer(CompactDisc cd){
	        this.cd = cd;
	    }
	    public void say(){
	        cd.play();
	    }
	}

Test class

	public class Test {
	    public static void main(String[] args) {
	        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("AutowiredBean.xml");
	        CDPlayer player = (CDPlayer) context.getBean("cdPlayer");
	        player.say();
	    }
	}

The result is 

	SgtPeppers playing…
