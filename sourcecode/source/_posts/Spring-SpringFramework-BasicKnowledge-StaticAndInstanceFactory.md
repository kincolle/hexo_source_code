---
title: Static And Instance Factory To Create Bean
date: 2018-07-28 21:57:13
tags:
categories:
- Spring
- SpringFramework
- Static And Instance Factory To Create Bean
---

## Use Static Factory To Create Bean
When use static factory to create beans, the class must be defined. But at this time class is not the implemend class fo bean but the static factory. because spring need to know which factory will be used for creating beans. We use factory-method to point the name of static factory function, and spring will invoke it to get a bean. By the way the factory-method must be static.  
 
Let's see an example:

First we define an interface:

	public interface Animal {	
	    public void sayHello();
	}

Then we have 2 implements:

	public class Cat implements Animal {
	    private String msg;
	    public void setMsg(String msg){
	        this.msg = msg;
	
	    }
	
	    @Override
	    public void sayHello(){
	        System.out.println(msg + "cat~cat~");
	    }
	}

	public class Dog implements Animal {
	    private String msg;
	    public void setMsg(String msg){
	        this.msg = msg;
	    }
	
	    @Override
	    public void sayHello(){
	        System.out.println(msg + "dog~dog~");
	    }
	}

The following AnimalFactory factory have a getAnimal static function, it will define which bean will be created by using the input param. 
    
	public clas AnimalFactory {
	    public static Animal getAnimal(String type){
	        if ("cat".equalsIgnoreCase(type)){
	            return new Cat();
	        } else {
	            return new Dog();
	        }
	    }
	}

In Spring, we use AnimalFactory to create Animal bean, here is the config file:

	<bean id="cat" class="com.kincolle.AnimalFactory" factory-method="getAnimal">
	    <constructor-arg value="cat" />
	    <property name="msg" value="The cat:" />
	</bean>
	<bean id="dog" class="com.kincolle.AnimalFactory" factory-method="getAnimal">
	    <constructor-arg value="dog" />
	    <property name="msg" value="The dog:" />
	</bean>


From the xml config file we know cat and dog can be get by using factory-method.

	public class Test {	
	    public static void main(String args[]){
	        ApplicationContext context = 
	                new ClassPathXmlApplicationContext("applicationContext.xml");
	        Animal a1 = context.getBean("cat", Animal.class);
	        a1.sayHello();
	        Animal a2 = context.getBean("dog", Animal.class);
	        a2.sayHello();
	    }
	}

The result will be:

	The cat:cat~cat~
	The dog:dog~dog~

## Use Instance Factory To Create Bean

There is only one differece betwenn Instance Factory and Static Factory: in the Instance Factory the factory is a bean.   

Here we have an AnimalFactory class like the following:

	ublic clas AnimalFactory {
	    public Animal getAnimal(String type){  // it is not static
	        if ("cat".equalsIgnoreCase(type)){
	            return new Cat();
	        } else {
	            return new Dog();
	        }
	    }
	}

The xml file will be:
	
	<bean id="animalFactory" class="com.kincolle.AnimalFactory" />
	<bean id="cat" factory-bean="animalFactory" factory-method="getAnimal">
	    <constructor-arg value="cat" />
	    <property name="msg" value="The cat:" />
	</bean>
	<bean id="dog" factory-bean="animalFactory" factory-method="getAnimal">
	    <constructor-arg value="dog" />
	    <property name="msg" value="The dog:" />
	</bean>

The result will be same.