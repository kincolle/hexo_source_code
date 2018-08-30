---
title: CompletableFuture
date: 2018-06-17 13:21:49
tags:
categories:
- Java Learning
- Concurrency
- CompletableFuture
---
## 1 Introduction Of Sync 
Future is a class which added in Java5. It is uesd for synchronized computing. The isDone is used for checking whether it completes and the get is user for blocking thread until computing completes or you can use cancel to stop computing.  

	public class BasicFuture {
	    public static void main(String[] args) throws ExecutionException, InterruptedException {
	        ExecutorService es = Executors.newFixedThreadPool(10);
	        Future<Integer> f = es.submit(() -> {
	            // Do sync and return result
	            return 1;
	        });
	        f.get();
	    }
	}

Even though the Future can do sync task, but it is hard to get the result because you only can get the result by blocking or polling. So, is there a better to get the result we want? The anwser is CompletableFuture. 

## 2 Introduction Of CompletableFuture
##### Sync
Fisrt, CompletableFuture implements Future interface, so you can use it as use Future. Second, CompletableFuture does not need to use thread pool to do sycn like the following:

	public static void test1() throws Exception{
        CompletableFuture<String> completableFuture=new CompletableFuture();
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("task doing...");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                //tell completableFuture the task has been done
                completableFuture.complete("result");
            }
        }).start();
        //Get result and it may block if does not finish the computing
        String result=completableFuture.get();
        System.out.println("result:"+result);
    }

##### Factory Method
###### 1. supplyAsync
We can write code to build CompletableFuture and get the result. Then, we also can use supplyAsync to get it: 

	public static void test2() throws Exception {
        CompletableFuture<String> completableFuture=CompletableFuture.supplyAsync(()->{
            System.out.println("task doing...");
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "result";
        });
        System.out.println("result:"+completableFuture.get());
    }

###### 2. allOf and anyOf  
The allOf function receive a CompletableFuture array and return a CompletableFuture<Void> instance when all CompletableFuture instance finish its task. The anyOf means return a CompletableFuture<Void> instance when any of all CompletableFuture instance finish its task.

	public static void test3() throws Exception {
        CompletableFuture<String> completableFuture1=CompletableFuture.supplyAsync(()->{
            System.out.println("task1 doing...");
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "result1";
        });

        CompletableFuture<String> completableFuture2=CompletableFuture.supplyAsync(()->{
            System.out.println("task2 doing...");
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "result2";
        });

        CompletableFuture<Object> anyResult=CompletableFuture.anyOf(completableFuture1,completableFuture2);

        System.out.println("result of the fisrt finished task:"+anyResult.get());

        CompletableFuture<Void> allResult=CompletableFuture.allOf(completableFuture1,completableFuture2);

        //wait until all task finished
        allResult.join();
        System.out.println("all task finished");

    }



