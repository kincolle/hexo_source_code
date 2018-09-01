---
title: SqlSession Interface and Executor Class
date: 2018-07-20 22:59:12
tags:
categories:
- Mybatis
- SqlSession Interface and Executor Class
---

## Introduction
When mybatis do operation, it will use implementaion of SqlSession interface.

    SqlSession session = sqlMapper.openSession(TransactionIsolationLevel.SERIALIZABLE);
    try {
      List<Author> authors = session.selectList("org.apache.ibatis.domain.blog.mappers.AuthorMapper.selectAllAuthors");
      assertEquals(2, authors.size());
    } finally {
      session.close();
	}



I'd like introduce selectList method of DefaultSqlSession class. 

    public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
      try {
        MappedStatement ms = configuration.getMappedStatement(statement);
        return executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
      } catch (Exception e) {
        throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
      } finally {
        ErrorContext.instance().reset();
      }
    }

We can get MappedStatement object from configuration and invoke query of Executor class to get the result we want. I have decribed that select node or update node is stored in MappedStatement. Which means, statement determines which one gonna execute select node.

The executor of DefaultSqlSession is setted in contruction method. So we need to checkout when the DefaultSqlSession class is being inited. The code is:


	public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
	     executorType = executorType == null ? defaultExecutorType : executorType;
	     executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
	     Executor executor;
	     if (ExecutorType.BATCH == executorType) {
	       executor = new BatchExecutor(this, transaction);
	     } else if (ExecutorType.REUSE == executorType) {
	       executor = new ReuseExecutor(this, transaction);
	     } else {
	       executor = new SimpleExecutor(this, transaction);
	     }
	     if (cacheEnabled) {
	       executor = new CachingExecutor(executor);
	     }
	     executor = (Executor) interceptorChain.pluginAll(executor);
	     return executor;
	}

As we can see there 3 types of Executor:

- ExecutorType.SIMPLE: Do not do special operation and every sql won't have a pretreatment SQL
- ExecutorType.REUSE: It will reuse pretreatment SQL
- ExecutorType.BATCH: Do things in batch

Configuration Class:

	protected ExecutorType defaultExecutorType = ExecutorType.SIMPLE;

So we know if we don't set the type of Executor class, the default will be SimpleExecutor class.

Also, we know in the selectList method of DefaultSqlSession class we will invoke query method of Executor finally.

	return executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);



BaseExecutor class：

	  public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException {
	    BoundSql boundSql = ms.getBoundSql(parameter);
	    CacheKey key = createCacheKey(ms, parameter, rowBounds, boundSql);
	    return query(ms, parameter, rowBounds, resultHandler, key, boundSql);
	  }


Like SimpleExecutor it does not extend Executor class but through BaseExecutor. So when invoke query method, it invokes query of BaseExecutor indeed.

SimpleExecutor类：

	public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
	     Statement stmt = null;
	     try {
	       Configuration configuration = ms.getConfiguration();
	       StatementHandler handler = configuration.newStatementHandler(wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
	       stmt = prepareStatement(handler, ms.getStatementLog());
	       return handler.<E>query(stmt, resultHandler) ; 
	     } finally {
	       closeStatement(stmt);
	     }
	   }
