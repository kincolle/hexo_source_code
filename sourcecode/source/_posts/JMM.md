---
title: JMM
date: 2018-02-28 17:49:28
tags:
categories:
- Java Learning
- Concurrency
- 2.JMM
---
# JMM
## 1 Abstraction of Java Memory Model
In java, all the instance area, static area and array are stored in stack memory, and the stack memory are shared among threads. Local variables, formal method parameters and exception handler parameters will no be shared among threads, so there will be no visibility problem and will not be affected by JMM.
Communication of threads is controlled by JMM, JMM can make a decision that which thread can write data into shared variables that are visible to others.It can be said that JMM define the relationship between thread and main memory: shared variables of threads are stored in main memory, every thread can have a local memory, and in local memory the replication are stored for write/read by this thread. JMM is an abstract conception and it does not really exist. It contains buffer, register and other hardwares. The JMM is showed as the following picture.

![](JMM/JMM.jpg)
    
according to the picture, if thread A and thread B want to communicate with each other, 2 steps must be done:<br>
1.first, thread A make the shared variables which are updated and stored in local memory of A into main memory.<br>
2.second, thread B get the shared data which has updated by thread a.
<br>

The following picture will show you the detail steps:<br>

![](JMM/communication.jpg)

As the picture shows, local memory A and B have replication of the share variable x.Persuming that at the first time in all three memoris the value of x is 0. Thread A update x into 1 and the value will be stored at local variable of A. when A and B want communicate, A when update main memory by using local variable of A, then in main memory the x is 1. Thread B go into main memory to get value of x which is 1 and update local variable of B, at this time the value of local variable of B is 1.<br>
We can see that A send message to B by using main memory. JMM control communication of main memory and every thread. to make sure the visibility of memory.<br>

## 2 Reordering
For higher performance of executing program, compiler and CPU always reorder the code. There are three types of reordering:<br>
1.compiler optimization reorder: Without change meaning of code, or instruction of single thread, compiler will changer order of execution.<br>
2.instruction paralell reorder: Modern CPU use Instruction-Level Parallelism to execute instructions. If there is no dependency, CPU can change order of instructions of execution.<br>
3.memory system reorder: Due to using of buffer of CPU, this make loading and storing execution seems execution at a random order.<br>
From java source to instruction of final execution, tree ordering has been done: reorder

![](JMM/reorder.jpg)

In the picture, No.1 is compiler optimization reorder, No.2 and No.3 is CPU reordering. These reordering may have problems when concurrency happen in memory. JMM is memory model with language level, at different platform, by forbiding some reordering it can make sure of visibility of memory.

## 4 CPU Reordering and Memory Barrier
Modern CPU use buffer to temporarily store data which will be store in main memory. Every buffer is just visible to its CPU。 That will affect the order of instructions to the memory: 

<table>
  <tr>
    <td width=50%> Processor A </td>
    <td width=50%> Processor B </td>
  </tr>
  <tr>
    <td width=50%> a = 1;//A1<br>x = b;//A2  </td>
    <td width=50%> b = 2;//A1<br>y = a;//A2 </td>
  <tr>
    <td width=100% colspan="2">  initial status: a=b=0<br> executed result: x=y=0 </td>
  </tr>
</table>

Persuming CPU A and CPU B access memory in the code order, but the final result is x=y=0. The detail reason is showed in the following picture.


