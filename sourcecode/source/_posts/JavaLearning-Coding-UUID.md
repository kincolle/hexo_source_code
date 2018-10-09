---
title: UUID
date: 2018-08-20 21:15:51
tags:
categories:
- Java Learning
- Coding
- UUID
---

The UUID is short for Universally Unique Identifier.

In java, there has 2 functions: randomUUID() and nameUUIDFromBytes(). The randomUUID() will create an UUID randomly which is always used for orderId. The nameUUIDFromBytes(byte[] n) will create an UUId according to the n, and it is always used for user checking like the identification. 

Here is an example: 
	
	import java.util.UUID;
	public class UuidDemo {
	    public static void main(String[] args) {
	
	
	        System.out.println(UUID.randomUUID().toString().replace("-", ""));
	        System.out.println(UUID.randomUUID().version());
	
	        System.out.println(UUID.nameUUIDFromBytes("890110866094329856".getBytes()).toString().replace("-", ""));
	        System.out.println(UUID.nameUUIDFromBytes("890110866094329856".getBytes()).version());
	
	    }
	}

The result will be:

    d9613ff9975b47e3a8bb1ef3766f7a86 
    4 
    873473466cf23b5fb988827f8dffbe7d 
    3
