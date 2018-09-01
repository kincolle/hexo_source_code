---
title: statementHandler
date: 2018-07-20 22:19:32
tags:
categories:
- Mybatis
- statementHandler
---
## Introduction
About statementHandler, no doubt it is the most important thing because it will connect to database. And in here we will use parameterHandler to make our SQL and use ResultHandler to get our result.


## statementHandler definition
Here let's see the definition of the statementHandler interface 
	
	
	public interface StatementHandler {
	 
	  Statement prepare(Connection connection)
	      throws SQLException;
	 
	  void parameterize(Statement statement)
	      throws SQLException;
	 
	  void batch(Statement statement)
	      throws SQLException;
	 
	  int update(Statement statement)
	      throws SQLException;
	 
	  <E> List<E> query(Statement statement, ResultHandler resultHandler)
	      throws SQLException;
	 
	  BoundSql getBoundSql();
	 
	  ParameterHandler getParameterHandler();
	 
	}

Here are some important method: prepare, parameterize ,query and update

In mybatis there are 4 implementions:

- RoutingStatementHandler: It won't have any detail implemention and will build different StatementHandler according to the type of the Executor.
- SimpleStatementHandler: It is used for Statement object of JDBC
- PreparedStatementHandler: It is used for pretreatment SQL
- CallableStatementHandler: It is used for procedure of SQL


In mybatis, Configuration object will use "new RoutingStatementHandler()" to build StatementHandlerc object. Which means, what we are really using is RoutingStatementHandler object and it will build statementHandler object according to the type of the Executor.   

Then we use function of statementHandler to do what we want.

## prepare function
It is used for compiling SQL. Let's the source code.


	 @Override
	  public Statement prepare(Connection connection) throws SQLException {
	    ErrorContext.instance().sql(boundSql.getSql());
	    Statement statement = null;
	    try {
	      statement = instantiateStatement(connection);
	      setStatementTimeout(statement);
	      setFetchSize(statement);
	      return statement;
	    } catch (SQLException e) {
	      closeStatement(statement);
	      throw e;
	    } catch (Exception e) {
	      closeStatement(statement);
	      throw new ExecutorException("Error preparing statement.  Cause: " + e, e);
	    }
	  }
	 
	  protected abstract Statement instantiateStatement(Connection connection) throws SQLException;

Definitely we concentrate on what does abstract function instantiateStatement do. Since it is an abstract method, it should has an implemetation. Let's see PreparedStatementHandler:

	@Override
	  protected Statement instantiateStatement(Connection connection) throws SQLException {
	    String sql = boundSql.getSql();
	    if (mappedStatement.getKeyGenerator() instanceof Jdbc3KeyGenerator) {
	      String[] keyColumnNames = mappedStatement.getKeyColumns();
	      if (keyColumnNames == null) {
	        return connection.prepareStatement(sql, PreparedStatement.RETURN_GENERATED_KEYS);
	      } else {
	        return connection.prepareStatement(sql, keyColumnNames);
	      }
	    } else if (mappedStatement.getResultSetType() != null) {
	      return connection.prepareStatement(sql, mappedStatement.getResultSetType().getValue(), ResultSet.CONCUR_READ_ONLY);
	    } else {
	      return connection.prepareStatement(sql);
	    }
	  }

It is simple, we can see it is used for compiling SQL.




##parameterize method

When we want to compile SQL, we need to set params and parameterize function of Statement will do it.

	@Override
	  public void parameterize(Statement statement) throws SQLException {
	    parameterHandler.setParameters((PreparedStatement) statement);
	  }

Apparently it implements by parameterHandler. 



## query/update method

After prepare and parameterize, we want operation the SQL we build.There are 2 kinds:

1. query
2. update


Those are simple,let's see the implemetation of  PreparedStatementHandler:

	@Override
	  public int update(Statement statement) throws SQLException {
	    PreparedStatement ps = (PreparedStatement) statement;
	    ps.execute();
	    int rows = ps.getUpdateCount();
	    Object parameterObject = boundSql.getParameterObject();
	    KeyGenerator keyGenerator = mappedStatement.getKeyGenerator();
	    keyGenerator.processAfter(executor, mappedStatement, ps, parameterObject);
	    return rows;
	  }
	 
	......
	@Override
	  public <E> List<E> query(Statement statement, ResultHandler resultHandler) throws SQLException {
	    PreparedStatement ps = (PreparedStatement) statement;
	    ps.execute();
	    return resultSetHandler.<E> handleResultSets(ps);
	  }


 