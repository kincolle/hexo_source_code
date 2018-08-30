---
title: Cache
date: 2018-06-21 22:51:32
tags:
categories:
- Mybatis
- Cache
---

## Introduction
There are 3 types cache in mybatis:

- SqlSession Cache
- Mapper Cache
- Customized Cache

## SqlSession Cache
MyBatis has open SqlSession Cache automatically. 

	public static void main(String[] args) {
        SqlSessionFactory factory = SqlSessionFactoryUtil.openSqlSession();

        // get SqlSession instance
        SqlSession sqlSession = factory.openSession();
        //get dao entity
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        // do the same select twice
        userMapper.selectByPrimaryKey(1);
        userMapper.selectByPrimaryKey(1);
        sqlSession.commit();

        System.out.println("\n\n=============================================================");
        // get a new SqlSession instance
        SqlSession sqlSession1 = factory.openSession();
        // do the same select
        sqlSession1.getMapper(UserMapper.class).selectByPrimaryKey(1);
        sqlSession.commit();
    }

The log will be

	DEBUG [main] - ooo Using Connection [com.mysql.jdbc.JDBC4Connection@77caeb3e]
	DEBUG [main] - ==>  Preparing: select user_ID from user where user_ID = ? 
	DEBUG [main] - ==> Parameters: 1(Integer)
	TRACE [main] - <==    Columns: user_ID
	TRACE [main] - <==        Row: 1, ASH-001
	DEBUG [main] - <==      Total: 1
	
	=============================================================
	DEBUG [main] - ooo Using Connection [com.mysql.jdbc.JDBC4Connection@553f17c]
	DEBUG [main] - ==>  Preparing: select user_ID from user where user_ID = ? 
	DEBUG [main] - ==> Parameters: 1(Integer)
	TRACE [main] - <==    Columns: user_ID
	TRACE [main] - <==         Row: 1, ASH-001
	DEBUG [main] - <==      Total: 1

## Mapper Cache
To use Mapper Cache, you need to set it by yourself.

	<cache/>

Then the result will be

	EBUG [main] - ooo Using Connection [com.mysql.jdbc.JDBC4Connection@5622fdf]
	DEBUG [main] - ==>  Preparing: select user_ID from user where user_ID = ? 
	DEBUG [main] - ==> Parameters: 1(Integer)
	TRACE [main] - <==    Columns: user_ID
	TRACE [main] - <==        Row: 1, AS-01
	DEBUG [main] - <==      Total: 1
	
	=============================================================

## Customized Cache
To use Customized Cache, you need to implement org.apache.ibatis.cache.Cache interface: