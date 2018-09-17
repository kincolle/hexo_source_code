---
title: Depends On
date: 2018-08-04 10:11:58
tags:
categories:
- Spring
- SpringFramework
- Depends On
---

If we have 2 classed, they have no relationship of DI but they have an order of init, then we need to use DependsOn of spring. For example, when the Read Bean inits it will read config and the Write Bean inits it will write config, so Read Bean must inits after Write Bean inits.    

 
Here we have a MockFileclass with a stataic field used for storing file: 

	public class MockFile {
	    private static String filecontent;
	
	    public static String getFilecontent() {
	        return filecontent;
	    }
	    public static void setFilecontent(String filecontent) {
	        MockFile.filecontent = filecontent;
	    }
	}

Create ReadWork.java:

	public class ReadWork {
	    public ReadWork() {
	        System.out.print("ReadWork constructor inits:");
	        System.out.println(MockFile.getFilecontent());
	    }
	}

Create WriteWork.java:

	public WriteWork() throws InterruptedException {
	    System.out.println("WriteWork constructor inits and write: property:better");
	    Thread.sleep(3000);
	    MockFile.setFilecontent("property:better");
	}

In beans.xml

	<bean id="writeWork" class="com.kincolle.depends.WriteWork" />
	<bean id="readWork" class="com.kincolle.depends.ReadWork"  />

Do inits

	public static void main(String[] args) throws Exception {
	    new ClassPathXmlApplicationContext("beans.xml");
	}


Run the program and the result will be:

First:

	WriteWork constructor inits and write: property:better

After 3 seconds:

	ReadWork constructor inits:property:better