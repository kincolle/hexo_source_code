---
title: Mapper Source Code
date: 2018-07-19 20:39:56
tags:
categories:
- Mybatis
- Mapper Source Code
---

## Introduction
About code of Mapper is in the package org.apache.ibatis.binding. MapperRegistry class is for Registing mapper interface and proxy instances.

	public class MapperRegistry {
	  //the config object
	  private Configuration config;
	  //A HashMap Key is a mapper object, and Value is a MapperProxyFactory object
	  //This MapperProxyFactory is the factory which build Mapper proxy object
	  private final Map<Class<?>, MapperProxyFactory<?>> knownMappers = new HashMap<Class<?>, MapperProxyFactory<?>>();
	  public MapperRegistry(Configuration config) {
	    this.config = config;
	  }
	  //get proxy object
	  @SuppressWarnings("unchecked")
	  public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
	    //through type of Mapper instance to get from Map
	    final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) knownMappers.get(type);
	    if (mapperProxyFactory == null)
	      throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
	    try {
	      return mapperProxyFactory.newInstance(sqlSession);
	    } catch (Exception e) {
	      throw new BindingException("Error getting mapper instance. Cause: " + e, e);
	    }
	  }
	
	  public <T> boolean hasMapper(Class<T> type) {
	    return knownMappers.containsKey(type);
	  }
	  //regist Mapper interface
	  public <T> void addMapper(Class<T> type) {
	    if (type.isInterface()) {
	      if (hasMapper(type)) {
	        throw new BindingException("Type " + type + " is already known to the MapperRegistry.");
	      }
	      boolean loadCompleted = false;
	      try {
	        knownMappers.put(type, new MapperProxyFactory<T>(type));
	        // It's important that the type is added before the parser is run
	        // otherwise the binding may automatically be attempted by the
	        // mapper parser. If the type is already known, it won't try.
	        MapperAnnotationBuilder parser = new MapperAnnotationBuilder(config, type);
	        parser.parse();
	        loadCompleted = true;
	      } finally {
	        if (!loadCompleted) {
	          knownMappers.remove(type);
	        }
	      }
	    }
	  }
	  public Collection<Class<?>> getMappers() {
	    return Collections.unmodifiableCollection(knownMappers.keySet());
	  }
	    ResolverUtil<Class<?>> resolverUtil = new ResolverUtil<Class<?>>();
	    resolverUtil.find(new ResolverUtil.IsA(superType), packageName);
	    Set<Class<? extends Class<?>>> mapperSet = resolverUtil.getClasses();
	    for (Class<?> mapperClass : mapperSet) {
	      addMapper(mapperClass);
	    }
	  }
	 
	  //Scan all interface by this package name
	  public void addMappers(String packageName) {
	    addMappers(packageName, Object.class);
	  }
	
	}

The getMapper method will invove newInstance method of the MapperProxyFactory class. From the source code we know before invoke getMapper(), the MapperProxyFactory will be inited. Let's the source code:  

	public class MapperProxyFactory<T> {
	  //The object of real Mapper interface
	  private final Class<T> mapperInterface;
	  //Cache of this interface, key is Method object, value is the real method
	  private Map<Method, MapperMethod> methodCache = new ConcurrentHashMap<Method, MapperMethod>();
	  public MapperProxyFactory(Class<T> mapperInterface) {
	    this.mapperInterface = mapperInterface;
	  }
	  public Class<T> getMapperInterface() {
	    return mapperInterface;
	  }
	  public Map<Method, MapperMethod> getMethodCache() {
	    return methodCache;
	  }
	  @SuppressWarnings("unchecked")
	  protected T newInstance(MapperProxy<T> mapperProxy) {
	    //Build a proxy 
	    return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[] { mapperInterface }, mapperProxy);
	  }
	  //Put into a sqlSession and build a proxy of Mapper
	  public T newInstance(SqlSession sqlSession) {
	    //Here build MapperProxy object, it implements InvocationHandler
	    final MapperProxy<T> mapperProxy = new MapperProxy<T>(sqlSession, mapperInterface, methodCache);
	    return newInstance(mapperProxy);
	  }
	}

Let's see the MapperProxy

	public class MapperProxy<T> implements InvocationHandler, Serializable {
	  private static final long serialVersionUID = -6424540398559729838L;
	  //SqlSession
	  private final SqlSession sqlSession;
	  //Type of interface
	  private final Class<T> mapperInterface;
	  //Cache of interface
	  private final Map<Method, MapperMethod> methodCache;
	  //Construction
	  public MapperProxy(SqlSession sqlSession, Class<T> mapperInterface, Map<Method, MapperMethod> methodCache) {
	    this.sqlSession = sqlSession;
	    this.mapperInterface = mapperInterface;
	    this.methodCache = methodCache;
	  }
	  //Proxy method
	  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
	    //Check whether if it is base method like toString(), hashCode() and so on
	    if (Object.class.equals(method.getDeclaringClass())) {
	      return method.invoke(this, args);
	    }
	    //Do cache in here
	    final MapperMethod mapperMethod = cachedMapperMethod(method);
	    //invoke mapperMethod.execute, here is the real invocation
	    return mapperMethod.execute(sqlSession, args);
	  }
	  //Cache
	  private MapperMethod cachedMapperMethod(Method method) {
	    MapperMethod mapperMethod = methodCache.get(method);
	    if (mapperMethod == null) {
	      mapperMethod = new MapperMethod(mapperInterface, method, sqlSession.getConfiguration());
	      methodCache.put(method, mapperMethod);
	    }
	    return mapperMethod;
	  }
	}

From the source code we know, we get MapperMethod  object from cache, if it does not exists, then build and put it into cache. Finally, invoke execute of MapperMethod.  
