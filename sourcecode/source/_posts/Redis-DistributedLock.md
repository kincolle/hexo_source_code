---
title: Distributed Lock
date: 2018-04-28 19:51:04
tags:
categories:
- Redis
- Distributed Lock
---

## Introduction of Distributed Lock 
In distributed situation, if we want to get Consistency of CAP, we can use Distributed Lock to implement it. There are 3 types of it:

- Database Distributed Lock
- Cache Distributed Lock
- Zookeeper Distributed Lock

Here I just want to introduce the Cache Distributed Lock implemented by using redis.

 
## Redis Distributed Lock
The Redis Distributed Lock will work like:

1. Try to get the lock, if return 0 which means the lock is already exists or we get a lock successfully.
2. To avoid a situation that after get lock the program go into an exception, we need to set a expire time to the lock
3. Release the lock and use DEL to delete the lock data

Here is an example:

##### get lock

	public class RedisTool {
	    private static final String LOCK_SUCCESS = "OK";
	    private static final String SET_IF_NOT_EXIST = "NX";
	    private static final String SET_WITH_EXPIRE_TIME = "PX";
	    /**
	     * get lock
	     * @param jedis Redis client
	     * @param lockKey 
	     * @param requestId 
	     * @param expireTime 
	     * @return whether success
	     */
	    public static boolean tryGetDistributedLock(Jedis jedis, String lockKey, String requestId, int expireTime) {
	        String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);
	        if (LOCK_SUCCESS.equals(result)) {
	            return true;
	        }
	        return false;
	    }
	}

##### release lock

	public class RedisTool {
	
	    private static final Long RELEASE_SUCCESS = 1L;
	
	    /**
	     * release lock
	     * @param jedis Redis client
	     * @param lockKey 
	     * @param requestId 
	     * @return whether success
	     */
	    public static boolean releaseDistributedLock(Jedis jedis, String lockKey, String requestId) {
	
	        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
	        Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId));
	
	        if (RELEASE_SUCCESS.equals(result)) {
	            return true;
	        }
	        return false;
	    }
	}