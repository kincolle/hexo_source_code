---
title: Circular Reference
date: 2018-08-16 23:21:46
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Circular Reference
---
## Circular Reference
In spring, there are 3 kinds of Circular Reference:

1. Constructor Circular Reference
2. Setter Circular Reference with singleton scope
3. Setter Circular Reference with prototype scope

Here I will introduce them.

### 1. Constructor Circular Reference
In spring container every bean id of creating bean will be put into a "current creating pool". It will be stored in the pool during the creating time. So if you find an id of a bean is in the pool when it is being created, an exception will come out. The BeanCurrentlyInCreationException means Circular Reference. Here let's init 3 beans.

StudentA POJO

	public class StudentA {
	 
	    private StudentB studentB ;
	 
	    public void setStudentB(StudentB studentB) {
	        this.studentB = studentB;
	    }
	 
	    public StudentA() {
	    }
	    
	    public StudentA(StudentB studentB) {
	        this.studentB = studentB;
	    }
	}

StudentB POJO

	public class StudentB {
	 
	    private StudentC studentC ;
	 
	    public void setStudentC(StudentC studentC) {
	        this.studentC = studentC;
	    }
	    
	    public StudentB() {
	    }
	 
	    public StudentB(StudentC studentC) {
	        this.studentC = studentC;
	    }
	}

StudentC POJO

	public class StudentC {
	 
	    private StudentA studentA ;
	 
	    public void setStudentA(StudentA studentA) {
	        this.studentA = studentA;
	    }
	 
	    public StudentC() {
	    }
	 
	    public StudentC(StudentA studentA) {
	        this.studentA = studentA;
	    }
	}

We can see StudentA depends on StudentB, StudentB depends on StudentC and StudentC depends on StudentA. Definetely there has a Circular Reference. Now let spring init them.

 	<bean id="a" class="com.kincolle.StudentA">
		<constructor-arg index="0" ref="b"></constructor-arg>
	</bean>
	<bean id="b" class="com.kincolle.StudentB">
		<constructor-arg index="0" ref="c"></constructor-arg>
	</bean>
	<bean id="c" class="com.kincolle.StudentC">
		<constructor-arg index="0" ref="a"></constructor-arg>
	</bean> 

Test class

	public class Test {
	    public static void main(String[] args) {
	        ApplicationContext context = new ClassPathXmlApplicationContext("com/kincolle/applicationContext.xml");
	    }
	}

The error message will be:

	Caused by: org.springframework.beans.factory.BeanCurrentlyInCreationException: 
		Error creating bean with name 'a': Requested bean is currently in creation: Is there an unresolvable circular reference?

### 2. Setter Circular Reference with singleton scope
First, let's change the xml to do setter DI with singleton scope:


	<bean id="a" class="com.kincolle.StudentA" scope="singleton">
		<property name="studentB" ref="b"></property>
	</bean>
	<bean id="b" class="com.kincolle.StudentB" scope="singleton">
		<property name="studentC" ref="c"></property>
	</bean>
	<bean id="c" class="com.kincolle.StudentC" scope="singleton">
		<property name="studentA" ref="a"></property>
	</bean>

Test class
	
	public class Test {
	    public static void main(String[] args) {
	        ApplicationContext context = new ClassPathXmlApplicationContext("com/kincolle/applicationContext.xml");
	        System.out.println(context.getBean("a", StudentA.class));
	    }
	}

The result is
	
	com.kincolle.StudentA@1fbfd6

You may wonder why there is no exception. The answer is:
> Spring will build a bean instance and will put it into map when it finished, then Spring will get the point to this bean which is not setted any reference. So, when beans of StudentA, StudentB and StudentC has been created successfully, the Circular Reference will get any problem.  

The following is the source code of implementation in DefaultSingletonBeanRegistry.java:

	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(64);
	
	private final Map<String, ObjectFactory> singletonFactories = new HashMap<String, ObjectFactory>(16);
	
	private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
	
	private final Set<String> registeredSingletons = new LinkedHashSet<String>(64);

	protected void addSingletonFactory(String beanName, ObjectFactory singletonFactory) {
		Assert.notNull(singletonFactory, "Singleton factory must not be null");
		synchronized (this.singletonObjects) {
			if (!this.singletonObjects.containsKey(beanName)) {
				this.singletonFactories.put(beanName, singletonFactory);
				this.earlySingletonObjects.remove(beanName);
				this.registeredSingletons.add(beanName);
			}
		}
	}

### 3. Setter Circular Reference with prototype scope
First, let's change the xml to do setter DI with prototype scope:

	<bean id="a" class="com.kincolle.StudentA" scope="prototype">
		<property name="studentB" ref="b"></property>
	</bean>
	<bean id="b" class="com.kincolle.StudentB" scope="prototype">
		<property name="studentC" ref="c"></property>
	</bean>
	<bean id="c" class="com.kincolle.StudentC" scope="prototype">
		<property name="studentA" ref="a"></property>
	</bean>

The scope="prototype" means every request will create a new bean. The most different point is: bean with status uses Prototype  and bean without status uses singleton.

Test class:

	public class Test {
	    public static void main(String[] args) {
	        ApplicationContext context = new ClassPathXmlApplicationContext("com/kincolle/applicationContext.xml");
	        System.out.println(context.getBean("a", StudentA.class));
	    }
	}

The result is:

	Caused by: org.springframework.beans.factory.BeanCurrentlyInCreationException: 
		Error creating bean with name 'a': Requested bean is currently in creation: Is there an unresolvable circular reference?

Why in prototype scope the error occur? Because bean with prototype scope can not be DIed. 