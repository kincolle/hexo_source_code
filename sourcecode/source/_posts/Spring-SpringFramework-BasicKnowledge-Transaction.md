---
title: Transaction
date: 2018-08-15 22:49:18
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Transaction
---

## Transaction
In computer science, ACID (Atomicity, Consistency, Isolation, Durability) is a set of properties of database transactions intended to guarantee validity even in the event of errors, power failures, etc. 

- Atomicity
> Transactions are often composed of multiple statements. Atomicity guarantees that each transaction is treated as a single "unit", which either succeeds completely, or fails completely: if any of the statements constituting a transaction fails to complete, the entire transaction fails and the database is left unchanged. An atomic system must guarantee atomicity in each and every situation, including power failures, errors and crashes.

- Consistency
>Consistency ensures that a transaction can only bring the database from one valid state to another, maintaining database invariants: any data written to the database must be valid according to all defined rules, including constraints, cascades, triggers, and any combination thereof. This prevents database corruption by an illegal transaction, but does not guarantee that a transaction is correct.

- Isolation
>Transactions are often executed concurrently (e.g., reading and writing to multiple tables at the same time). Isolation ensures that concurrent execution of transactions leaves the database in the same state that would have been obtained if the transactions were executed sequentially. Isolation is the main goal of concurrency control; depending on the method used, the effects of an incomplete transaction might not even be visible to other transactions.

- Durability
>Durability guarantees that once a transaction has been committed, it will remain committed even in the case of a system failure (e.g., power outage or crash). This usually means that completed transactions (or their effects) are recorded in non-volatile memory.

## Propagation

- PROPAGATION_REQUIRED 
>This attribute tells that the code needs to be run in a transactional context. If a transaction already exists then the code will use it otherwise a new transaction is created. This is the default and mostly widely used transaction setting.

- PROPAGATION_SUPPORTS 
>If a transaction exists then the code will use it, but the code does not require a new one. As an example, consider a ticket reservation system. A query to get total seats available can be executed non-transactionally. However, if used within a transaction context it will deduct tickets already selected and reduce them from the total count, and hence may give a better picture. This attribute should be used with care especially when PROPAGATION_REQUIRED or PROPAGATION_REQUIRES_NEW is used within a PROPAGATION_SUPPORTS context.

- PROPAGATION_MANDATORY 
>Participates in an existing transaction, however if no transaction context is present then it throws a TransactionRequiredException

- PROPAGATION_REQUIRES_NEW 
>Creates a new transaction and if an existing transaction is present then it is suspended. In other words a new transaction is always started. When the new transaction is complete then the original transaction resumes. This transaction type is useful when a sub activity needs to be completed irrespective of the containing transaction. The best example of this is logging. Even if a transaction roll backs you still want to preserve the log statements. Transaction suspension may not work out of the box with all transaction managers, so make sure that the transaction manager supports transaction suspension

- PROPAGATION_NOT_SUPPORTED 
>This attribute says that transaction is not supported. In other words the activity needs to be performed non-transactionally. If an existing transaction is present then it is suspended till the activity finishes.

- PROPAGATION_NEVER 
>This attributes says that the code cannot be invoked within a transaction. However, unlike PROPAGATION_NOT_SUPPORTED, if an existing transaction is present then an exception will be thrown

- PROPAGATION_NESTED 
> The code is executed within a nested transaction if existing transaction is present, if no transaction is present then a new transaction is created. Nested transaction is supported out of the box on only certain transaction managers.

## Example
Here we simulate a stock buying:

The Account POJO:

	public class Account {
	 
		private int accountid;
		private String name;
		private String balance;
		
		
		public int getAccountid() {
			return accountid;
		}
		public void setAccountid(int accountid) {
			this.accountid = accountid;
		}
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public String getBalance() {
			return balance;
		}
		public void setBalance(String balance) {
			this.balance = balance;
		}
	}


The stock POJO:

	public class Stock {
	 
		private int stockid;
		private String name;
		private Integer count;
		
		public Stock() {
			super();
		}
		 
		public Stock(int stockid, String name, Integer count) {
			super();
			this.stockid = stockid;
			this.name = name;
			this.count = count;
		}
	 
		public int getStockid() {
			return stockid;
		}
	 
		public void setStockid(int stockid) {
			this.stockid = stockid;
		}
	 
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public Integer getCount() {
			return count;
		}
		public void setCount(Integer count) {
			this.count = count;
		}
		
	}


AccountDao


	public interface AccountDao {
	 
		void addAccount(String name,double money);
		
		void updateAccount(String name,double money,boolean isbuy);
		
	}

Implementation of the AccountDao:
	
	public class AccountDaoImpl extends JdbcDaoSupport implements AccountDao {
	 
		@Override
		public void addAccount(String name, double money) {
			String sql = "insert account(name,balance) values(?,?);";
			this.getJdbcTemplate().update(sql,name,money);
			
		}
	 
		@Override
		public void updateAccount(String name, double money, boolean isbuy) {
			String sql = "update account set balance=balance+? where name=?";
			if(isbuy)
				sql = "update account set balance=balance-? where name=?";
			this.getJdbcTemplate().update(sql, money,name);
		}
		
	}

StockDao

	public interface StockDao {
		
		void addStock(String sname,int count);
		
		void updateStock(String sname,int count,boolean isbuy);
	 
	}

Implementation of the StockDao:


	public class StockDaoImpl extends JdbcDaoSupport implements StockDao {
	 
		@Override
		public void addStock(String sname, int count) {
			String sql = "insert into stock(name,count) values(?,?)";
			this.getJdbcTemplate().update(sql,sname,count);
		}
	 
		@Override
		public void updateStock(String sname, int count, boolean isbuy) {
			String sql = "update stock set count = count-? where name = ?";
			if(isbuy)
				sql = "update stock set count = count+? where name = ?";
			this.getJdbcTemplate().update(sql, count,sname);
		}
		
	}

Service interface

	public interface BuyStockService {
	 
		public void addAccount(String accountname, double money);
		
		public void addStock(String stockname, int amount);
		
		public void buyStock(String accountname, double money, String stockname, int amount) throws BuyStockException;
		
	}

Implementation of the Service interface:


	public class BuyStockServiceImpl implements BuyStockService{
		
		private AccountDao accountDao;
		private StockDao stockDao;
		
		@Override
		public void addAccount(String accountname, double money) {
			accountDao.addAccount(accountname,money);
		}
	 
		@Override
		public void addStock(String stockname, int amount) {
			stockDao.addStock(stockname,amount);
		}
	 
		@Override
		public void buyStock(String accountname, double money, String stockname, int amount) throws BuyStockException {
			boolean isBuy = true;
			accountDao.updateAccount(accountname, money, isBuy);
			if(isBuy==true){
				throw new BuyStockException("error happened when buying stock");
			}
				stockDao.updateStock(stockname, amount, isBuy);
		}
	 
		public AccountDao getAccountDao() {
			return accountDao;
		}
	 
		public void setAccountDao(AccountDao accountDao) {
			this.accountDao = accountDao;
		}
	 
		public StockDao getStockDao() {
			return stockDao;
		}
	 
		public void setStockDao(StockDao stockDao) {
			this.stockDao = stockDao;
		}
		
	}

Costomized exception:

	public class BuyStockException extends Exception {
	 
		public BuyStockException() {
			super();
		}
	 
		public BuyStockException(String message) {
			super(message);
		}
	 
	}


XML for TransactionProxyFactoryBean:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns:context="http://www.springframework.org/schema/context"
	    xmlns:mvc="http://www.springframework.org/schema/mvc"
	    xmlns:aop="http://www.springframework.org/schema/aop"
	    xmlns:tx="http://www.springframework.org/schema/tx"
	    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
	        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
	        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-aop-4.2.xsd
	        ">
		
		<context:property-placeholder location="classpath:jdbc.properties"/>
		
		<bean id="dataSource" class="com.kincolle.DataSource"  >
			 <property name="driverClass" value="${jdbc.driverClass}"></property>
			 <property name="jdbcUrl"  value="${jdbc.url}"></property>
	         <property name="user"  value="${jdbc.username}"></property>
	         <property name="password" value="${jdbc.password}"></property>
		</bean>
		
		<bean id="accountDao" class="com.kincolle.dao.AccountDaoImpl">
			<property name="dataSource" ref="dataSource"/>
		</bean>
		
		<bean id="stockDao" class="com.kincolle.dao.StockDaoImpl">
			<property name="dataSource" ref="dataSource"/>
		</bean>
		
		<bean id="buyStockService" class="com.kincolle.service.BuyStockServiceImpl">
			<property name="accountDao" ref="accountDao"></property>
			<property name="stockDao" ref="stockDao"></property>
		</bean>
		
		<bean id="myTracnsactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
			<property name="dataSource" ref="dataSource"></property>
		</bean>
		
		<bean id="serviceProxy" class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean">
			<property name="transactionManager" ref="myTracnsactionManager"></property>
			<property name="target" ref="buyStockService"></property>
			<property name="transactionAttributes">
				<props>
					<prop key="add*">ISOLATION_DEFAULT,PROPAGATION_REQUIRED</prop>
					<prop key="buyStock">ISOLATION_DEFAULT,PROPAGATION_REQUIRED,-BuyStockException</prop>
				</props>
			</property>
			
		</bean>
			
	</beans>  

Test

	public static void main(String[] args) {
		String resouce = "com/kincolle/applicationContext.xml";
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext(resouce);
		BuyStockService buyStockService =  (BuyStockService) applicationContext.getBean("serviceProxy");		
		try {
			buyStockService.buyStock("kincolle", 1000, "ctrip", 100);
		} catch (BuyStockException e) {
			e.printStackTrace();
		}
	}

Then the transaction will let error happen.


