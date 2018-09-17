---
title: Async
date: 2018-08-08 21:01:08
tags:
categories:
- Spring
- SpringFramework
- MultiThread
- Async
---
## Config @Async
If we use Java config we just need to add @EnableAsync on the config class like the following:

	@Configuration
	@EnableAsync
	public class SpringAsyncConfig { ... }

If we use xml the like the following:

	<task:executor id="myexecutor" pool-size="5"  />
	<task:annotation-driven executor="myexecutor"/>

## Function without return
The following is an example that use @Asycn with no return.

	@Async
	public void asyncMethodWithVoidReturnType() {
	    System.out.println("Execute method asynchronously. "
	      + Thread.currentThread().getName());
	}

## Function with return
Also you can define return when use @Async

	@Async
	public Future<String> asyncMethodWithReturnType() {
	    System.out.println("Execute method asynchronously - "
	      + Thread.currentThread().getName());
	    try {
	        Thread.sleep(5000);
	        return new AsyncResult<String>("hello world !!!!");
	    } catch (InterruptedException e) {
	        //
	    }
	
	    return null;
	}

## Executor
In default situation, Spring uses SimpleAsyncTaskExecutor to run async tasks. We can use our own executor to do it.

Set a executor in config class

	@Configuration
	@EnableAsync
	public class SpringAsyncConfig {
	
	    @Bean(name = "threadPoolTaskExecutor")
	    public Executor threadPoolTaskExecutor() {
	        return new ThreadPoolTaskExecutor();
	    }
	}

Then input name of the exexcutor when use @Async

	@Async("threadPoolTaskExecutor")
	public void asyncMethodWithConfiguredExecutor() {
	    System.out.println("Execute method with configured executor - "
	      + Thread.currentThread().getName());
	}

## Global Executor
The config class implements AsyncConfigurer interface which means the executor from getAsyncExecutor() will be the default executor. 

	@Configuration
	@EnableAsync
	public class SpringAsyncConfig implements AsyncConfigurer {
	    @Override
	    public Executor getAsyncExecutor() {
	        return new ThreadPoolTaskExecutor();
	    }
	}