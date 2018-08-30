---
title: Automatically Change DataSource By Using Spring
date: 2018-06-11 21:43:18
tags:
categories:
- Mybatis
- Automatically Change DataSource By Using Spring
---

## Multi DataSource
In real project, a service may use more than one data source. Here I will introduce how to change data source aotomatically by using Spring.

## (1) Spring Config
Here are Spring config

	<context:component-scan base-package="kincolle.service,kincolle.aop" />
    <context:component-scan base-package="kincolle.aop" />
    <!-- properties file -->
    <context:property-placeholder location="classpath:config/db.properties" />

    <!-- Data Source Config -->
    <bean name="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc_url}" />
        <property name="username" value="${jdbc_username}" />
        <property name="password" value="${jdbc_password}" />
        <property name="initialSize" value="0" />
        <property name="maxActive" value="20" />
        <property name="maxIdle" value="20" />
        <property name="minIdle" value="0" />
        <property name="maxWait" value="60000" />
    </bean>
    
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource" />
      <property name="configLocation" value="classpath:config/mybatis-config.xml" />
      <property name="mapperLocations" value="classpath*:config/mappers/**/*.xml" />
    </bean>
    
    <!-- Transaction manager for a single JDBC DataSource -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
    
    <!-- Define transaction by using annotation -->
    <tx:annotation-driven transaction-manager="transactionManager" /> 
    
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="basePackage" value="kincolle.mapper" />
      <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>

    <!-- Enables the use of the @AspectJ style of Spring AOP -->
    <aop:aspectj-autoproxy/>
    
    <!-- ===============The second data source config=============== -->
    <bean name="dataSource_2" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc_url_2}" />
        <property name="username" value="${jdbc_username_2}" />
        <property name="password" value="${jdbc_password_2}" />
        <property name="initialSize" value="0" />
        <property name="maxActive" value="20" />
        <property name="maxIdle" value="20" />
        <property name="minIdle" value="0" />
        <property name="maxWait" value="60000" />
    </bean>
    
    <bean id="sqlSessionFactory_slave" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource_2" />
      <property name="configLocation" value="classpath:config/mybatis-config-2.xml" />
      <property name="mapperLocations" value="classpath*:config/mappers2/**/*.xml" />
    </bean>
    
    <!-- Transaction manager for a single JDBC DataSource -->
    <bean id="transactionManager_2" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource_2" />
    </bean>
    
    <!-- Define transaction by using annotation -->
    <tx:annotation-driven transaction-manager="transactionManager_2" /> 
    
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="basePackage" value="kincolle.mapper2" />
      <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory_2"/>
    </bean>

Like the code shows, we config 2 data sources and 2 transactionManagers.

## (2) Using AbstractRoutingDataSource and AOP

(1)

First of all, we define a enum to mean different data source.

	/**
	 * Datasource:master/slave
	 */
	public enum DataSources {
	    MASTER, SLAVE
	}

(2)

Using thread local to store data source key in every thread:

	public class DataSourceTypeManager {
	    private static final ThreadLocal<DataSources> dataSourceTypes = new ThreadLocal<DataSources>(){
	        @Override
	        protected DataSources initialValue(){
	            return DataSources.MASTER;
	        }
	    };
	    
	    public static DataSources get(){
	        return dataSourceTypes.get();
	    }
	    
	    public static void set(DataSources dataSourceType){
	        dataSourceTypes.set(dataSourceType);
	    }
	    
	    public static void reset(){
	        dataSourceTypes.set(DataSources.MASTER0);
	    }
	}

(3) 

Define ThreadLocalRountingDataSource and extends AbstractRoutingDataSource:

	public class ThreadLocalRountingDataSource extends AbstractRoutingDataSource {
	    @Override
	    protected Object determineCurrentLookupKey() {
	        return DataSourceTypeManager.get();
	    }
	}

(4) 

Input master and slave data source into ThreadLocalRountingDataSource in config file

		<context:component-scan base-package="kincolle.service,kincolle.aop" />
	    <context:component-scan base-package="kincolle.aop" />
	    <!-- properties file -->
	    <context:property-placeholder location="classpath:config/db.properties" />    
	    <!-- Data Source Master -->
	    <bean name="dataSourceMaster" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
	        <property name="url" value="${jdbc_url}" />
	        <property name="username" value="${jdbc_username}" />
	        <property name="password" value="${jdbc_password}" />
	        <property name="initialSize" value="0" />=
	        <property name="maxActive" value="20" />
	        <property name="maxIdle" value="20" />
	        <property name="minIdle" value="0" />
	        <property name="maxWait" value="60000" />
	    </bean>    
	    <!-- Data Source Slave -->
	    <bean name="dataSourceSlave" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
	        <property name="url" value="${jdbc_url_slave}" />
	        <property name="username" value="${jdbc_username_slave}" />
	        <property name="password" value="${jdbc_password_slave}" />
	        <property name="initialSize" value="0" />
	        <property name="maxActive" value="20" />
	        <property name="maxIdle" value="20" />
	        <property name="minIdle" value="0" />
	        <property name="maxWait" value="60000" />
	    </bean>    
	    <bean id="dataSource" class="kincolle.util.ThreadLocalRountingDataSource">
	        <property name="defaultTargetDataSource" ref="dataSourceMaster" />
	        <property name="targetDataSources">
	            <map key-type="kincolle.enums.DataSources">
	                <entry key="MASTER" value-ref="dataSourceMaster"/>
	                <entry key="SLAVE" value-ref="dataSourceSlave"/>
	            </map>
	        </property>
	    </bean>    
	    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	      <property name="dataSource" ref="dataSource" />
	      <property name="configLocation" value="classpath:config/mybatis-config.xml" />
	      <property name="mapperLocations" value="classpath*:config/mappers/**/*.xml" />
	    </bean>    
	    <!-- Transaction manager for a single JDBC DataSource -->
	    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	        <property name="dataSource" ref="dataSource" />
	    </bean>    
	    <!-- Define transaction by using annotation -->
	    <tx:annotation-driven transaction-manager="transactionManager" /> 
	    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	      <property name="basePackage" value="kincolle.mapper" />
	      <!-- <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/> -->
	    </bean>

(5) 

Using AOP to set Datasource key.

	@Aspect    // for aop
	@Component // for auto scan
	@Order(0)  // execute before @Transactional
	public class DataSourceInterceptor {    
	    @Pointcut("execution(public * kincolle.service..*.getUser(..))")
	    public void dataSourceSlave(){};
	    
	    @Before("dataSourceSlave()")
	    public void before(JoinPoint jp) {
	        DataSourceTypeManager.set(DataSources.SLAVE);
	    }
	    // ... ...
	}

(6)

 AbstractRoutingDataSource

ThreadLocalRountingDataSource extends AbstractRoutingDataSource and implements abstract method "protected abstract Object determineCurrentLookupKey()" to route different data source. We analyze it from its source code:

	public abstract class AbstractRoutingDataSource extends AbstractDataSource implements InitializingBean

AbstractRoutingDataSource implements InitializingBean, then when init bean in Spring, it will call "void afterPropertiesSet() throws Exception;" of InitializingBean. Here let's see how it works:

    @Override
    public void afterPropertiesSet() {
        if (this.targetDataSources == null) {
            throw new IllegalArgumentException("Property 'targetDataSources' is required");
        }
        this.resolvedDataSources = new HashMap<Object, DataSource>(this.targetDataSources.size());
        for (Map.Entry<Object, Object> entry : this.targetDataSources.entrySet()) {
            Object lookupKey = resolveSpecifiedLookupKey(entry.getKey());
            DataSource dataSource = resolveSpecifiedDataSource(entry.getValue());
            this.resolvedDataSources.put(lookupKey, dataSource);
        }
        if (this.defaultTargetDataSource != null) {
            this.resolvedDefaultDataSource = resolveSpecifiedDataSource(this.defaultTargetDataSource);
        }
    }

targetDataSources is the dataSourceMaster and the dataSourceSlave we set in XML config file. The afterPropertiesSet mehtod is used for setting dataSourceMaster and  dataSourceSlave to build a HashMap——resolvedDataSources. Then we can get datasource from the map by using key. Let's see the "Connection getConnection() throws SQLException; " in AbstractDataSource.

	@Override
    public Connection getConnection() throws SQLException {
        return determineTargetDataSource().getConnection();
    }

and about determineTargetDataSource()

    protected DataSource determineTargetDataSource() {
        Assert.notNull(this.resolvedDataSources, "DataSource router not initialized");
        Object lookupKey = determineCurrentLookupKey();
        DataSource dataSource = this.resolvedDataSources.get(lookupKey);
        if (dataSource == null && (this.lenientFallback || lookupKey == null)) {
            dataSource = this.resolvedDefaultDataSource;
        }
        if (dataSource == null) {
            throw new IllegalStateException("Cannot determine target DataSource for lookup key [" + lookupKey + "]");
        }
        return dataSource;
    }

We implements "Object lookupKey = determineCurrentLookupKey(); " and save key in ThreadLocal. After get key we get datasource from map which is in resolvedDataSources by using key. And 
the key in ThreadLocal is set by using AOP.