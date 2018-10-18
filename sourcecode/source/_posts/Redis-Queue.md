---
title: Queue
date: 2018-03-14 20:27:52
tags:
categories:
- Redis
- Queue
---

## Redis Queue
In redis we can use push and pop to implement queue function.

Here is the example:

	redis> LRANGE alpha 0 -1         
	1) "a"
	2) "b"
	3) "c"
	4) "d"
	
	redis> RPOPLPUSH alpha reciver   
	"d"
	
	redis> LRANGE alpha 0 -1
	1) "a"
	2) "b"
	3) "c"
	
	redis> LRANGE reciver 0 -1
	1) "d"
	
	redis> RPOPLPUSH alpha reciver   
	"c"
	
	redis> LRANGE alpha 0 -1
	1) "a"
	2) "b"
	
	redis> LRANGE reciver 0 -1
	1) "c"
	2) "d"
	
	redis> LRANGE number 0 -1
	1) "1"
	2) "2"
	3) "3"
	4) "4"
	
	redis> RPOPLPUSH number number
	"4"
	
	redis> LRANGE number 0 -1           
	1) "4"
	2) "1"
	3) "2"
	4) "3"
	
	redis> RPOPLPUSH number number
	"3"
	
	redis> LRANGE number 0 -1           
	1) "3"
	2) "4"
	3) "1"
	4) "2"
 
