---
title: Garbage Collector
date: 2018-07-17 22:25:29
tags:
categories:
- Java Learning
- GC
- Garbage Collector
---

# Introduction
For garbage collector, we have collector for young generation and old generation.

![](JavaLearning-GC-GarbageCollector/1.png)


## Serial
Serial Collector is for young generation, and it uses Mark-Copy algorithm. Since it has only one thread to do the collection, so it will stop all works which is call STW(Stop The World).
　　
![](JavaLearning-GC-GarbageCollector/2.png)
 

## ParNew
The difference of ParNew from Serial is that it used multi-thread to do the collection. That means it does not need do STW.

![](JavaLearning-GC-GarbageCollector/3.png)

## Parallel Scavenge 
Parallel Scavenge uses Mark-Copy algorithm. Parallel Scavenge concentrates on how to short the time of collecting.

![](JavaLearning-GC-GarbageCollector/4.png)