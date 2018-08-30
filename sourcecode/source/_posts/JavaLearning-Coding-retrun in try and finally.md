---
title: Return In Try and Finally
date: 2018-06-17 23:23:52
tags:
categories:
- Java Learning
- Coding
- Return In Try and Finally
---
## Introduction
In a try{} there has a return sentense, and after try there has finally{}. So, if the code return sucsessfully, will code in finally{} be operated? If it does, when?  

The anwser is code in finally{} will be operated before return unless System.exit(0) is in try{}. The return will be operated firstly but will not return imediately. It will go to do finally{} then do return.

## example

	public class Test
	{
	       public static void main(String[] args)
	       {
	              System.out.println(new Test().test());;
	       }
	
	       static int test()
	       {
	              int x = 1;
	              try
	              {
	                     return x;
	              }
	              finally
	              {
	                     System.out.println("finally is running:" + ++x);
	              }
	       }
	} 

The result will be :

>finally is running:2
>
>1

From the result we know that in finally{} x has been 2. But the result from test() is still 1.