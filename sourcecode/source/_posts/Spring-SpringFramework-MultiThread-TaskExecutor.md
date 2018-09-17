---
title: TaskExecutor
date: 2018-08-11 22:15:39
tags:
categories:
- Spring
- SpringFramework
- MultiThread
- TaskExecutor
---
## Introduction
In spring we use TaskExecutor to do mutilthread tasks. The ThreadPoolTaskExecutor can implements a TaskExecutor with a thread pool.

## Example

#### Java config

	@Configuration
	@ComponentScan("kincolle.TaskExecutor")
	@EnableAsync 
	public class TaskExecutorConfig implements AsyncConfigurer{
	    @Override
	    public Executor getAsyncExecutor() {
	        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
	        taskExecutor.setCorePoolSize(5);
	        taskExecutor.setMaxPoolSize(10);
	        taskExecutor.setQueueCapacity(25);
	        taskExecutor.initialize();
	        return taskExecutor;
	    }
	
	    @Override
	    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
	        return null;
	    }
	}

#### Execute class

	@Service
	public class AsyncTaskService {
	
	    @Async
	    public void executeAsyncTask(Integer i){
	        System.out.println("executeAsyncTask:"+i);
	    }
	
	    @Async
	    public void executeAsyncTaskPlus(Integer i){
	        System.out.println("executeAsyncTaskPlus:"+i);
	    }
	
	}

#### Run class


	public class Main {
	    public static void main(String[] args) {
	        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(TaskExecutorConfig.class);
	
	        AsyncTaskService asyncTaskService = context.getBean(AsyncTaskService.class);
	        for(int i=0;i<10;i++){
	            asyncTaskService.executeAsyncTaskPlus(i);
	            asyncTaskService.executeAsyncTask(i+1);
	        }
	        context.close();
	
	    }
	}

#### Result
	
	executeAsyncTask:1
	executeAsyncTask:2
	executeAsyncTask:3
	executeAsyncTask:4
	executeAsyncTaskPlus:2
	executeAsyncTaskPlus:3
	executeAsyncTaskPlus:5
	executeAsyncTask:6
	executeAsyncTaskPlus:6
	executeAsyncTask:7
	executeAsyncTaskPlus:7
	executeAsyncTask:8