---
title: Singleton
date: 2018-05-18 21:45:27
tags:
categories:
- Design Pattern
- CreateType
- Singleton
---
## Singleton
In software engineering, the singleton pattern is a software design pattern that restricts the instantiation of a class to one object. This is useful when exactly one object is needed to coordinate actions across the system. The concept is sometimes generalized to systems that operate more efficiently when only one object exists, or that restrict the instantiation to a certain number of objects. 

Here is the java code example:


	public class Singleton {
	    private static Singleton singleton= new Singleton();
	    private Singleton(){}
	    public static Singleton  getInstance(){
	        return Singleton.singleton;
	    }
	}

This the an example for concurrency:

	public class SingletonWithProblemOfConcurrency {
	    private static SingletonWithProblemOfConcurrency singletonWithProblemOfConcurrency = null;
	    private SingletonWithProblemOfConcurrency(){}
	    public static SingletonWithProblemOfConcurrency getInstance(){
	        if(singletonWithProblemOfConcurrency==null)
	            synchronized (singletonWithProblemOfConcurrency){
	                if(singletonWithProblemOfConcurrency==null){
	                    //with JMM problem
	                    singletonWithProblemOfConcurrency=new SingletonWithProblemOfConcurrency();
	                    return singletonWithProblemOfConcurrency;
	                }
	            }
	        return singletonWithProblemOfConcurrency;
	    }
	}