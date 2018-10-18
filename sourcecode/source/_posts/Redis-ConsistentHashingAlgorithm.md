---
title: Consistent Hashing Algorithm
date: 2018-09-03 23:13:48
tags:
categories:
- Redis
- Consistent Hashing Algorithm
---

## Introduction of Consistent Hashing Algorithm
The Consistent hashing can guarantee that when a cache machine is removed, only the objects cached in it will be rehashed; when a new cache machine is added, only a fairly few objects will be rehashed.

##### Hash space
Commonly, a hash function will map a value into a 32-bit key, 0~2^32-1. Now imagine mapping the range into a circle, then the key will be wrapped, and 0 will be followed by 2^32-1, as illustrated in the following picture.

![](Redis-ConsistentHashingAlgorithm/1.jpg)

##### Map object into hash space
Now consider four objects: object1~object4. We use a hash function to get their key values and map them into the circle, as illustrated in the following picture.

![](Redis-ConsistentHashingAlgorithm/2.jpg)
 
##### Map the cache into hash space
The basic idea of consistent hashing is to map the cache and objects into the same hash space using the same hash function.Now consider we have three caches, A, B and C, and then the mapping result will look like in the following picture.

![](Redis-ConsistentHashingAlgorithm/3.jpg)

##### Map objects into cache
Now all the caches and objects are hashed into the same space, so we can determine how to map objects into caches. Take object obj for example, just start from where obj is and head clockwise on the ring until you find a server. If that server is down, you go to the next one, and so forth. See the picture above. According to the method, object1 will be cached into cache A; object2 and object3 will be cached into cache C, and object4 will be cached into cache B.

##### Add or remove cache
Now consider the two scenarios, a cache is down and removed; and a new cache is added. If cache B is removed, then only the objects that cached in B will be rehashed and moved to C; in the example, seeobject4 illustrated in the following picture.

![](Redis-ConsistentHashingAlgorithm/4.jpg)

If a new cache D is added, and D is hashed between object2 and object3 in the ring, then only the objects that are between D and B will be rehashed; in the example, see object2, illustrated in the following picture.

![](Redis-ConsistentHashingAlgorithm/5.jpg)

##### Virtual nodes
It is possible to have a very non-uniform distribution of objects between caches if you don't deploy enough caches. The solution is to introduce the idea of "virtual nodes".

Virtual nodes are replicas of cache points in the circle, each real cache corresponds to several virtual nodes in the circle; whenever we add a cache, actually, we create a number of virtual nodes in the circle for it; and when a cache is removed, we remove all its virtual nodes from the circle.

Consider the above example. There are two caches A and C in the system, and now we introduce virtual nodes, and the replica is 2, then three will be 4 virtual nodes. Cache A1 and cache A2 represent cache A; cache C1 and cache C2 represent cache C, illustrated as in the following picture.

![](Redis-ConsistentHashingAlgorithm/6.jpg)

Then, the map from object to the virtual node will be:

![](Redis-ConsistentHashingAlgorithm/7.jpg)

When you get the virtual node, you get the cache, as in the above figure.So object1 and object2 are cached into cache A, and object3 and object4 are cached into cache. The result is more balanced now.