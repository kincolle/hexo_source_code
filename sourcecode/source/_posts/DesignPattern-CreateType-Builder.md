---
title: Builder
date: 2018-05-13 9:16:57
tags:
categories:
- Design Pattern
- CreateType
- Builder
---
## Builder
The Builder is a design pattern designed to provide a flexible solution to various object creation problems in object-oriented programming. The intent of the Builder design pattern is to separate the construction of a complex object from its representation. It is one of the Gang of Four design patterns.

Here is the java code example:

interface Sender

	public interface Sender {
	    public void send();
	}

class MailSender 

	public class MailSender implements Sender {
	    @Override
	    public void send(){
	        System.out.println("This is Mailsender");
	    }
	}

class SmsSender

	public class SmsSender implements Sender {
	    @Override
	    public void send(){
	        System.out.println("This is SmssSender");
	    }
	}

class Builder

	public class Builder {
	    private List<Sender> list = new ArrayList<>();
	
	    public void produceMailSender(int number) {
	        for(int i=0;i<number;i++){
	            list.add(new MailSender());
	        }
	    }
	
	    public void produceSmsSender(int number) {
	        for(int i=0;i<number;i++){
	            list.add(new SmsSender());
	        }
	    }
	}

Then do the test

	public class BuilderTest {
	    public static void main(String[] args) {
	        Builder builder = new Builder();
	        builder.produceMailSender(5);
	        builder.produceMailSender(5);
	    }
	}