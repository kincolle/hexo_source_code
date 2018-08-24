---
title: Proxy
date: 2018-05-23 8:36:21
tags:
categories:
- Design Pattern
- Structure
- Proxy
---
## 1 Proxy
A proxy, in its most general form, is a class functioning as an interface to something else. The proxy could interface to anything: a network connection, a large object in memory, a file, or some other resource that is expensive or impossible to duplicate. In short, a proxy is a wrapper or agent object that is being called by the client to access the real serving object behind the scenes. Use of the proxy can simply be forwarding to the real object, or can provide additional logic. In the proxy, extra functionality can be provided, for example caching when operations on the real object are resource intensive, or checking preconditions before operations on the real object are invoked. For the client, usage of a proxy object is similar to using the real object, because both implement the same interface.

Here is the java code example

![](DesignPattern-Structure-Proxy/1.jpg)

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

class Proxy 

	public class Proxy implements Sourceable {
	
	    private Source source;
	    public Proxy(){
	        super();
	        this.source = new Source();
	    }
	    @Override
	    public void method() {
	        before();
	        source.method();
	        atfer();
	    }
	    private void atfer() {
	        System.out.println("after proxy!");
	    }
	    private void before() {
	        System.out.println("before proxy!");
	    }
	}

Then do the test

	public class ProxyTest {
	    public static void main(String[] args) {
	        Sourceable source = new Proxy();
	        source.method();
	    }
	}



