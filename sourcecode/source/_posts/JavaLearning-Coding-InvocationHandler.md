---
title: InvocationHandler
date: 2018-05-24 22:13:32
tags:
categories:
- Java Learning
- Coding
- InvocationHandler
---
## InvocationHandler
Here is the introduction in Oracle:

>
InvocationHandler is the interface implemented by the invocation handler of a proxy instance.
Each proxy instance has an associated invocation handler. When a method is invoked on a proxy instance, the method invocation is encoded and dispatched to the invoke method of its invocation handler.
	
Here is an example:

	import java.lang.reflect.*;
	
	public class Main {
	    static void customer(ProxyInterface pi){
	        pi.say();
	    }
	    public static void main(String[] args){
	        RealObject real = new RealObject();
	        ProxyInterface proxy = (ProxyInterface)Proxy.newProxyInstance(ProxyInterface.class.getClassLoader(),new Class[]{ProxyInterface.class}, new ProxyObject(real));
	        customer(proxy);
	    }
	}
	
	
	interface ProxyInterface{
	    void say();
	}
	
	class RealObject implements ProxyInterface{
	    public void say(){
	        System.out.println("i'm talking");
	    }
	}
	
	//implement InvocationHandler 
	class ProxyObject implements InvocationHandler{
	    private Object proxied = null;
	    public ProxyObject(){
	        
	    }
	    public ProxyObject(Object proxied){
	        this.proxied  = proxied;
	    }
	    public Object invoke(Object arg0, Method arg1, Object[] arg2) throws Throwable {
	        System.out.println("hello");
	        return arg1.invoke(proxied, arg2);
	    };
	}

Here we can see the proxy class implements an InvocationHandler interface. We can use newProxyInstance to get a instance then use it as a param.