---
title: Concurrency-Java-SyncControl
date: 2018-03-18 18:33:26
tags:
categories:
- Java Learning
- Concurrency
- 5.Concurrency-Java-SyncControl
---

## 1 ReenterLock
The ReenterLock can replace synchronized totally. It is implemented in java.util.concurrent.locks.ReentrantLock. Here is a code example of it:

	public class ReenterLock implements Runnable{
	    public static ReentrantLock lock = new ReentrantLock();
	    public static int i = 0;
	    @Override
	    public void run() {
	        for(int j = 0;j<10000000;j++){
	            lock.lock();
	            lock.lock();
	            try {
	                i++;
	            }
	            finally {
	                lock.unlock();
	                lock.unlock();
	            }
	
	        }
	    }
	
	    public static void main(String[] args) throws InterruptedException {
	        ReenterLock tl = new ReenterLock();
	        Thread t1 = new Thread(tl);
	        Thread t2 = new Thread(tl);
	        t1.start();
	        t2.start();
	        t1.join();
	        t2.join();
	        System.out.println(i);
	    }
	}

From the 7th line to 12th line of the code, ReentrantLock is used for protecting i, and it makes sure that operation for i is thread safe. Compare to synchronized, developer should lock and unlock by hinself. That is the reason  flexibility of ReentrantLock is better than that of synchronized. By the way, when using ReentrantLock you must release the lock when you finished your job or the critical section will never be accessed.<br>
You may wonder why it is called ReentrantLock, Because you can lock the same thing more than one time. That is why in the code, the "lock.lock();" and the "lock.unlock();" was used twice.<br>
##### Interruption
In synchronized, if a thread is waiting on a lock, there only 2 results:(1) get the lock it wants; ()keep on waiting. Btt in ReentrantLock, there is a3rd result, that is the interruption. That means while waiting on a lock, the waiting can be canceled. This may be helpful for deadlock. Here is an example of it.

	public class IntLock implements Runnable{
	    public static ReentrantLock lock1 = new ReentrantLock();
	    public static ReentrantLock lock2 = new ReentrantLock();
	    int lock;
	
	    public IntLock(int lock){
	        this.lock=lock;
	    }
	
	    @Override
	    public void run(){
	        try{
	            if(lock == 1) {
	                lock1.lockInterruptibly();
	                try {
	                    Thread.sleep(500);
	                }catch (InterruptedException e){}
	                lock2.lockInterruptibly();
	            }else {
	                lock2.lockInterruptibly();
	                try{
	                    Thread.sleep(500);
	                } catch (InterruptedException e) {
	                    e.printStackTrace();
	                }
	                lock1.lockInterruptibly();
	            }
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }finally {
	            if(lock1.isHeldByCurrentThread())
	                lock1.unlock();
	            if(lock2.isHeldByCurrentThread())
	                lock2.unlock();
	            System.out.println(Thread.currentThread().getId()+":the thread is out");
	        }
	    }
	
	    public static void main(String[] args) throws InterruptedException {
	        IntLock r1 = new IntLock(1);
	        IntLock r2 = new IntLock(2);
	        Thread t1 = new Thread(r1);
	        Thread t2 = new Thread(r2);
	        t1.start();
	        t2.start();
	        Thread.sleep(1000);
	        t2.interrupt();
	    }
	} 

After t1 and t2 started, t1 takes lock1 then takes lock2; t2 takes lock2 then takes lock1. So, there is a deadlock apparantly. Here, requesting for lock uses lockInterruptible() function. Then, the waiting on lock can be canceled.<br>
At the 47th code, since main thread is sleeping, the 2 threads are in deadlock. At the 49th code, due to t2 is interrupted, t2 will give up waiting on lock1 and release lock2. Then t1 will operate successfully.

##### Waiting Time Limit    
For avoidiung deadlock, there is a way call Waiting Time Limit. Most time, we can not know why we can't get the lock we want and starvation happens. If giving a Waiting Time Limit and let the thread give up automatically. That is meaningful for system. We can use tryLock() to do a Waiting Time Limit. Here is an example of it。

	public class TimeLock implements Runnable{
	    public static ReentrantLock lock = new ReentrantLock();
	    @Override
	    public void run() {
	        try{
	            if(lock.tryLock(5, TimeUnit.SECONDS)){
	                Thread.sleep(6000);
	            }else{
	                System.out.println("get lock failed");
	            }
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }finally {
	            if(lock.isHeldByCurrentThread()) lock.unlock();
	        }
	    }
	
	    public static void main(String[] args) {
	        TimeLock tl = new TimeLock();
	        Thread t1 = new Thread(tl);
	        Thread t2 = new Thread(tl);
	        t1.start();
	        t2.start();
	    }
	}

Here, tryLock() get 2 parameers:(1) the first one is the length of time (2) another is the time unit we use. Here our time unit is second and the length is 5. It means the thread will waiting on lock at most 5 seconds. If it gets lock successfully in 5 seconds it will return true and return false if not. Here it will waiting for 6 seconds, so it will fail.<br> 
ReentrantLock.tryLock() can operate without any parameter. At this situation, the current thread will immediately get lock which is not being taken and return true. If the lock is being taken then do not wait and return false immediately. At this model, there is no deadlock. Here is an example:

	 public class TryLock implements Runnable {
	    public static ReentrantLock lock1 = new ReentrantLock();
	    public static ReentrantLock lock2 = new ReentrantLock();
	    int lock;
	
	    public TryLock(int lock){
	        this.lock=lock;
	    }
	    @Override
	    public void run() {
	        if(lock == 1 ){
	            while(true){
	                if(lock1.tryLock()){
	                    try {
	                        try {
	                            Thread.sleep(500);
	                        } catch (InterruptedException e) {
	                            e.printStackTrace();
	                        }
	                        if(lock2.tryLock()){
	                            try {
	                                System.out.println(Thread.currentThread().getId()+":My job done");
	                                return;
	                            }finally {
	                                lock2.unlock();
	                            }
	                        }
	                    }finally {
	                        lock1.unlock();
	                    }
	                }
	            }
	        }else {
	            while (true){
	                if(lock2.tryLock()){
	                    try {
	                        try {
	                            Thread.sleep(500);
	                        }catch (InterruptedException e){
	                        }
	                        if(lock1.tryLock()){
	                            try {
	                                System.out.println(Thread.currentThread().getId()+":My job done");
	                                return;
	                            }finally {
	                                lock1.unlock();
	                            }
	                        }
	                    }finally {
	                        lock2.unlock();
	                    }
	                }
	            }
	        }
	    }
	
	    public static void main(String[] args) {
	        TryLock r1 = new TryLock(1);
	        TryLock r2 = new TryLock(2);
	        Thread t1 = new Thread(r1);
	        Thread t2 = new Thread(r2);
	        t1.start();
	        t2.start();
	    }
	}

In the code, t1 and t2 are at deadlock situation. But since tryLock() is used, the they will not waiting forever.

##### FairLock
At the most situation, requesting for lock is not fair. For example, thread 1 requests lock A then thread 2 requests lock A too. When lock A is useful, which one should get the Lock A. System will choose one randomly. So it is not fair. But fairLock is not like this. It will operate in a order: first in first out. That means there will be no starvation. If we use synchornized to control lock, it will be unfair. ReentrantLock can let us set the fairness. Here is the constructor:

	public ReentrantLock(boolean fair)

When the parameter fair is true, it means the lock is fair. The fairLock looks good, but it needs a ordered queue. So, performance of fairLock is not good. Here is an exmaple of fairLock:

	public class FairLock implements Runnable{
	    public static ReentrantLock fairLock = new ReentrantLock(true);
	    @Override
	    public void run() {
	        while (true){
	            try{
	                fairLock.lock();
	                System.out.println(Thread.currentThread().getName()+" get the lock");
	            }finally {
	                fairLock.unlock();
	            }
	        }
	    }
	
	    public static void main(String[] args) {
	        FairLock fr = new FairLock();
	        Thread t1 = new Thread(fr,"t1");
	        Thread t2 = new Thread(fr,"t2");
	        t1.start();
	        t2.start();
	    }
	}

At the second code, it sets the lock to be fair. Then, there are 2 threads t1 and t2 requesting the lock. Here is the result:

	t1 get the lock
	t2 get the lock
	t1 get the lock
	t2 get the lock

Here we can see it is ordered.

About ReentrantLock, here is some points:

- lock(): request lock, if the lock is being taken then wait
- lockInterruptibly(): request lock, but interruption first.
- tryLock(): try to get lock, return true if succeeds and false if fails immediately
- tryLock(longt time, TimeUnit unit): try to get lock, return true if succeeds and false if fails in the limit time
- unlock(): release lock

## 2 Condition
I have introduced Object.wait() and Object.notify(), Condition object is like them. Object.wait() and Object.notify() is always used with sycnchronized. But Condition is always used with ReentrantLock. Through "Condition newCondition()" of Lock interface we can create a ReentrantLock with Condition.<br> 
There are some functions of Condition I want to introduce:

- await(): it will let then current thread wait and release the lock the current thread has taken. When other threads use signal() or signalAll(), it will go on.
- awaitUninterruptibly(): It is like await() and will not be interrupted.
- signal(): it will signal one thread. It is like Object.notify(). 

Here is an example of it:

	public class ReenterLockCondition implements Runnable{
	    public static ReentrantLock lock = new ReentrantLock();
	    public static Condition condition = lock.newCondition();
	    @Override
	    public void run() {
	        try{
	            lock.lock();
	            condition.await();
	            System.out.println("Thread is going on");
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }finally {
	            lock.unlock();
	        }
	    }
	
	    public static void main(String[] args) throws InterruptedException {
	        ReenterLockCondition tl = new ReenterLockCondition();
	        Thread t1 = new Thread(tl);
	        t1.start();
	        Thread.sleep(200);
	
	        lock.lock();
	        condition.signal();
	        System.out.println("before unlock the lock");
	        lock.unlock();
	        System.out.println("after unlock the lock");
	    }
	}

At the 3rd line of the code, it create a Contition instance. At the 8th line of the code, request Condition object to wait.   


## 3 Semaphore
The semaphore is used for cooperation of threads. It is extension of Lock. Both synchronized and ReentrantLock only allow one request succeed to get the resource. But the semaphore can allow more than one thread to access one resource. Here is the constructor of semaphore:

	public Semaphore(int permits)
	public Semaphore(int permits, boolean fair)

When constructs a semaphore, must input the number of thread that can be allowed.<br>
Here is a example of semaphore:

	public class SemapDemo implements  Runnable{
	    final Semaphore semp = new Semaphore(5);
	    @Override
	    public void run() {
	        try{
	            semp.acquire();
	
	            Thread.sleep(2000);
	            System.out.println(Thread.currentThread().getId()+":done");
	            semp.release();
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }
	    }
	
	    public static void main(String[] args) {
	        ExecutorService exec = Executors.newFixedThreadPool(20);
	        final SemapDemo demo = new SemapDemo();
	        for(int i=0;i<20;i++){
	            exec.submit(demo);
	        }
	    }
	}
From the 7th line to the 9th line of the code is the critical section code, program will limit the number of threads. At the 2nd line of the code set 5. That means 5 threads can go into the critical section code at the same time. Requesting semaphore is using acquire() and releasing semaphore is using release(). It is the same logic of lock. 

## 4 ReadWriteLock
ReadWriteLock is a lock that it can split read and write. It will reduce the competition for resources and advance performance of system. It is easy to understand, like, A1, A2, A3 want to write, and B1, B2, B3 want to read. If  using synchronized or ReentrantLock, theoretically ReadAndRead, ReadAndWrite, WriteAndWrite is serialization operation. When B1 read data, B2 and B3 must wait. But reading do not change data, so it is not unreasonable. That is why ReadWriteLock has been made.<br>
Here is the rules:

- read-read: do not block
- read-write: read block write, write will block read too
- write-write: block

In the system, if number of reading is larger than that of writing, then using ReadWriteLock will be helpful. Here is an example:

	public class ReadWriteLockDemo {
	    private static Lock lock = new ReentrantLock();
	    private static ReentrantReadWriteLock readWriteLock =new ReentrantReadWriteLock();
	    private static Lock readLock =readWriteLock.readLock();
	    private static Lock writeLock = readWriteLock.writeLock();
	    private int value;
	
	    public Object handleRead(Lock lock) throws InterruptedException{
	        try{
	            lock.lock();
	            Thread.sleep(1000);
	            return value;
	        }finally {
	            lock.unlock();
	        }
	    }
	
	    public void handleWrite(Lock lock,int index) throws InterruptedException{
	        try {
	            lock.lock();
	            Thread.sleep(1000);
	            value=index;
	        }finally {
	            lock.unlock();
	        }
	    }
	
	    public static void main(String[] args) {
	        final ReadWriteLockDemo demo = new ReadWriteLockDemo();
	        Runnable readRunnable = new Runnable() {
	            @Override
	            public void run() {
	                try{
	                    demo.handleRead(readLock);
	                    demo.handleRead(lock);
	                } catch (InterruptedException e) {
	                    e.printStackTrace();
	                }
	            }
	        };
	        Runnable writuRunnable = new Runnable() {
	            @Override
	            public void run() {
	                try{
	                    demo.handleWrite(writeLock,new Random().nextInt());
	                    demo.handleWrite(lock,new Random().nextInt());
	                } catch (InterruptedException e) {
	                    e.printStackTrace();
	                }
	            }
	        };
	
	        for(int i=0;i<18;i++){
	            new Thread(readRunnable).start();
	        }
	
	        for (int i = 18; i<20;i++){
	            new Thread(writuRunnable).start();
	        }
	    }
	}

At the 11th line and 21 line of code simulate a low performance operating. They are reading and writing. At the 34th line and 45 line of code are the read thread and the write thread. Here, At the 34th line use readLock and At the 35th line writeLock. From 53th to 55th lines, create 18 read threads and from 57th to 59th lines create  2 write threads. Due to using ReadWriteLock, so the reading is parallel and write will be blocked. So the code will finish in about 2 seconds.

## 5 CountDownLatch
The CountDownLatch can let a thread do not operate until a count is over. Here is an example of it.

	public class CountDownLatchDemo implements Runnable {
	    static final CountDownLatch end = new CountDownLatch(10);
	    static final CountDownLatchDemo demo = new CountDownLatchDemo();
	
	    @Override
	    public void run() {
	        try{

	            Thread.sleep(new Random().nextInt(10)*1000);
	            System.out.println("check complete");
	            end.countDown();
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }
	    }
	    public static void main(String[] args) throws InterruptedException {
	        ExecutorService exec  = Executors.newFixedThreadPool(10);
	        for(int i=0;i<10;i++){
	            exec.submit(demo);
	        }

	        end.await();

	        System.out.println("fire");
	        exec.shutdown();
	    }
	}

At the 2nd line of the code, create a CountDownLatch instance. The count number is 10. That means only when 10 threads finished, thread on the CountDownLatch can start to operate.

## 6 CyclicBarrier
The CyclicBarrier is like CountDownLatch but it is more powerful and complex than CountDownLatch. It can be used for more than one time and that is what cyclic means.<br>
Here is an example of it:

	public class CyclicBarrierDemo {
	    public static class Soldier implements Runnable{
	        private String soldier;
	        private final CyclicBarrier cyclic;
	
	        Soldier(CyclicBarrier cyclic, String soldierName){
	            this.cyclic=cyclic;
	            this.soldier=soldierName;
	        }
	
	        @Override
	        public void run() {
	            try {
	                //wait for all soldiers
	                cyclic.await();
	                doWork();
					//wait for all soldiers finish work
					cyclic.await();
	            } catch (InterruptedException e) {
	                e.printStackTrace();
	            } catch (BrokenBarrierException e) {
	                e.printStackTrace();
	            }
	        }

	        void doWork(){
	            try {
	                Thread.sleep(Math.abs(new Random().nextInt()%10000));
	            } catch (InterruptedException e) {
	                e.printStackTrace();
	            }
	            System.out.println(soldier+":mission completed");
	        }
	    }
	
	    public static class BarrierRun implements Runnable{
	        boolean flag;
	        int N;
	        public BarrierRun(Boolean flag,int N){
	            this.flag=flag;
	            this.N=N;
	        }
	
	        @Override
	        public void run() {
	            if(flag){
	                System.out.println("Commander:"+N+"soldiers are mission completed");
	            }else{
	                System.out.println("Commander:"+N+"soldiers are assembled");
	                flag=true;
	            }
	        }
	    }
	
	    public static void main(String[] args) {
	        final int N=10;
	        Thread[] allSoldier = new Thread[N];
	        boolean flag =false;
	        CyclicBarrier cyclic = new CyclicBarrier(N,new BarrierRun(flag,N));
			//set the barrier point
	        System.out.println("assemble");
	        for(int i = 0 ; i<N;++i){
	            System.out.println(N+"th soldier report");
	            allSoldier[i]=new Thread(new Soldier(cyclic,"soldier"+i));
	            allSoldier[i].start();
	        }
	    }
	}

