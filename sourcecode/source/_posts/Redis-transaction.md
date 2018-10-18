---
title: Transaction 
date: 2018-09-02 21:41:04
tags:
categories:
- Redis
- Transaction
---

## Introduction of Transaction 
Here is the introduction of [redis transaction](https://redis.io/topics/transactions)

MULTI, EXEC, DISCARD and WATCH are the foundation of transactions in Redis. They allow the execution of a group of commands in a single step, with two important guarantees:

- All the commands in a transaction are serialized and executed sequentially. It can never happen that a request issued by another client is served in the middle of the execution of a Redis transaction. This guarantees that the commands are executed as a single isolated operation.

- Either all of the commands or none are processed, so a Redis transaction is also atomic. The EXEC command triggers the execution of all the commands in the transaction, so if a client loses the connection to the server in the context of a transaction before calling the MULTI command none of the operations are performed, instead if the EXEC command is called, all the operations are performed. When using the append-only file Redis makes sure to use a single write(2) syscall to write the transaction on disk. However if the Redis server crashes or is killed by the system administrator in some hard way it is possible that only a partial number of operations are registered. Redis will detect this condition at restart, and will exit with an error. Using the redis-check-aof tool it is possible to fix the append only file that will remove the partial transaction so that the server can start again.

## Example
##### EXEC

	127.0.0.1:6379> MULTI
	OK
	127.0.0.1:6379> SET key1 1
	QUEUED
	127.0.0.1:6379> HSET key2 field1 1
	QUEUED
	127.0.0.1:6379> SADD key3 1
	QUEUED
	127.0.0.1:6379> EXEC
	1) OK
	2) (integer) 1
	3) (integer) 1

##### DISCARD

	127.0.0.1:6379> MULTI
	OK
	127.0.0.1:6379> SET key1 1
	QUEUED
	127.0.0.1:6379> DISCARD
	OK
	127.0.0.1:6379> EXEC
	(error) ERR EXEC without MULTI

##### WATCH
	
	127.0.0.1:6379> WATCH key1
	OK
	127.0.0.1:6379> set key1 2
	OK
	127.0.0.1:6379> MULTI
	OK
	127.0.0.1:6379> set key1 3
	QUEUED
	127.0.0.1:6379> set key2 3
	QUEUED
	127.0.0.1:6379> EXEC
	(nil)

