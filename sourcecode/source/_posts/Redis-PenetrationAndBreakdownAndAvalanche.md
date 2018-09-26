---
title: Penetration, breakdown And Avalanche
date: 2018-05-19 20:32:09
tags:
categories:
- Redis
- Penetration, breakdown And Avalanche

---

I have read a good article about Penetration, breakdown And Avalanche of cache in [here](http://www.codeblogbt.com/archives/148143)

Since the birth of the first caching framework Memcached, caching has been widely used in Internet applications. If your application traffic is small, using caching may not require extra consideration. But if your traffic reaches hundreds of millions, you will have to think deeply.Caching problem: cache penetration, cache breakdown and cache avalanche.

## Penetration
Cache penetration refers to querying a data that must not exist, because the data does not exist, so it will never be cached, so each request will request the database.

For example, we request a user data with UserID as -1, because the user does not exist, so the request reads the database every time. In such a case, if some people who are not in the right way to use this loophole to forge a large number of requests, DB will probably not be able to afford it.So the large amount of traffic is hung up.

There are several solutions for cache penetration, one is prevention in advance, and the other is prevention afterwards.

Prevention in advance. In fact, all requests are checked by parameter checking, which keeps most illegal requests out of the outermost layer. In our example, we are doing parameter verification, and all requests for UserID less than 0 are rejected. But even if we did a full parameter check,There may be some escape from the net. There will be some unexpected situations.

For example, our UserID is increasing, then if someone requests a large user information of a UserID (for example, 1000000), and our UserID is 10000. At this time, you can’t limit UserID’s greater than that.10 thousand is illegal, or more than 100 thousand is illegal, so the user ID can certainly check through parameters. But the user does not exist, so every request will ask the database.

In fact, the above is just a situation that I can think of, and there must be a lot of things we didn’t expect. What we can do for these situations is time to prevent.

Prevention after the event. After the hindsight, we still cache the empty result when we query an empty result, but set a short expiration time (for one minute, for example). Here we can see that in fact, we did not completely prevent illegal requests, but the risk of illegal requests.Let the redis with stronger capacity to undertake the database with less tolerance will be more secure.

Through the above two processing methods, we can basically solve the problem of cache penetration. In advance, 80% illegal requests were prevented and the remaining 20% illegal requests were transferred by Redis.

## Breakdown
If your application has some hot data with high access, we usually put it in the cache to increase the access speed. In addition, in order to maintain timeliness, we usually set an expiration date. But for these highly visited KEY, we need to consider one problem: when hot KEYAt the moment of failure, does massive requests generate large amounts of database requests, resulting in a database crash?

For example, we have a business KEY, which has 10000 concurrent requests for KEY. When the KEY fails, there will be 10 thousand threads requesting the database update cache. Without proper measures at this time, the database is likely to collapse.

In fact, the above problem is the problem of cache breakdown, which occurs in the expiration of cache KEY. In this case, there are two commonly used solutions: exclusive locks, never expired.

mutex
Mutex refers to when the cache KEY expires to update, let the program get the lock first, only the thread that gets the lock is eligible to update the cache KEY. Other threads that do not get the lock will sleep for a while before getting the latest cache data. In this way, at the same time, there will always be only one.The thread will read the database, thus avoiding the impact of massive database requests on the database.

For the locks mentioned above, we can use some of the principles provided by the cache to complete the operation. For example, for redis caching, we can use its SETNX command to complete.

public String get(key) {
String value = redis.get(key);
if (value == null) { //Cache expiration
if (redis.setnx(key_mutex, 1, 1 * 60) == 1) {
value = db.get(key);
redis.set(key, value, expireTime);
redis.del(key_mutex);
} else {
//Retry after a few moments of dormancy
sleep(50);
get(key);
}
} else {
return value;
}
}
The key_mutex above is actually an ordinary KEY-VALUE value. We use the setnx command to set its value to 1. If someone updates the cache KEY at this time, the setnx command will return 0, indicating that the settings failed..

Never expire
From the point of view of caching, if you set up never to expire, there will be no massive requests for databases. At this time, we usually update the data from the database to the cache by a new way of starting a thread. The more mature way is to synchronize the number of caching and database by the timing task.According to it.

But this kind of solution will delay the data, that is, the data read by the thread is not the latest data. But for the general Internet function, a little delay is acceptable.

## Avalanche
Caching avalanche refers to the same expiration time when we set up the cache, which causes the cache to fail at the same time at the same time. All the requests are forwarded to the database, which eventually leads to the collapse of the transient pressure of the database.

For example, we have 1000 KEY, and each KEY has 10 concurrent requests. The cache avalanche refers to the failure of the 1000 KEY at the same time, and then suddenly 1000 * 10 = ten thousand queries..

The problems caused by cache avalanche are generally difficult to investigate. If there is no advance prevention, it is likely that it will take a lot of effort to find the cause. For the caching avalanche, the simplest solution is to add a random time (for example, 1-5 minutes) on the basis of the original failure time, so that each cache is overdue.The repetition rate will be reduced, thereby reducing the occurrence of cache avalanche.

summary
For the three scenarios of cache penetration, cache breakdown, and buffering avalanche, many people will not understand or even get confused.

「Cache penetration refers to data that does not exist, which makes the cache virtually useless, and the cache layer is penetrated. For example, we request a user data with UserID as -1, because the user does not exist, so the request reads the database every time. In this case, if some heartsPeople who are not in the right track to make use of this loophole to forge large numbers of requests are likely to cause DB to not be able to cope with such large traffic and hang up.

「”Cache breakdown” refers to KEY with high concurrence. When the KEY fails, there are many requests to request the database at the same time and update the cache. For example, we have a business KEY, which has 10000 concurrent requests for KEY. When the KEY fails, it will beThere are 10 thousand threads that request the database update cache. Without proper measures at this time, the database is likely to collapse.

「Cache avalanche means that the cache expires at the same time, just like all snow blocks fall at the same time, like avalanche. For example, we have 1000 KEY, and each KEY has 10 concurrent requests. And the caching avalanche refers to the 1000 KEY failed at the same time, and suddenly there were 1000 * 10 = ten thousand queries.

We can make some summaries of their occurrence.

「”Cache penetration” is a loophole at the business level resulting in illegal requests, which is not related to the amount of requests and cache invalidation. “Cache breakdown” will only appear on the hot spot data, which is not related to the business at the moment of cache failure. The “cache avalanche” is caused by multiple KEY failures at the same time, resulting in database requests.Too much. Non hot data can also lead to cache avalanche, as long as KEY fails at the same time.