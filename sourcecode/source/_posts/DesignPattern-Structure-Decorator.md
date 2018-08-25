---
title: Decorator
date: 2018-05-20 23:52:32
tags:
categories:
- Design Pattern
- Structure
- Decorator
---
## Decorator
In object-oriented programming, the decorator pattern is a design pattern that allows behavior to be added to an individual object, dynamically, without affecting the behavior of other objects from the same class. The decorator pattern is often useful for adhering to the Single Responsibility Principle, as it allows functionality to be divided between classes with unique areas of concern. The decorator pattern is structurally nearly identical to the chain of responsibility pattern, the difference being that in a chain of responsibility, exactly one of the classes handles the request, while for the decorator, all classes handle the request.

Here is the java code example:

![](DesignPattern-Structure-Decorator/1.jpg)

interface Sourceable

	public interface Sourceable {
	    public void method();
	}

class Source

	public class Source implements Sourceable {
	    @Override
	    public void method() {
	        System.out.println("This is the original method.");
	    }
	}

class Decorator 

	public class Decorator implements Sourceable {
	    private Sourceable source;
	    public Decorator(Sourceable source){
	        super();
	        this.source=source;
	    }
	    @Override
	    public void method() {
	        System.out.println("before");
	        source.method();
	        System.out.println("after");
	    }
	}

Then do the test

	public class DecoratorTest {
	    public static void main(String[] args) {
	        Sourceable source = new Source();
	        Decorator decorator =new Decorator(source);
	        decorator.method();
	    }
	}
