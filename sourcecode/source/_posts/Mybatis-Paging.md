---
title: Paging
date: 2018-07-08 23:25:58
tags:
categories:
- Mybatis
- Paging
---

## Introduction
In mybatis, there are some ways to page. And I will describe them.

## Array
You can get all data into a array and get the real data you want from the array.

Dao interface:

	List<Student> queryStudentsByArray();

StudentMapper.xml

	<select id="queryStudentsByArray"  resultMap="studentmapper">
	       select * from student
	</select>


Then get real data：
 	
    public List<Student> queryStudentsByArray(int currPage, int pageSize) {
        List<Student> students = studentMapper.queryStudentsByArray();
        int firstIndex = (currPage - 1) * pageSize;
        int lastIndex = currPage * pageSize;
        return students.subList(firstIndex, lastIndex);
    }

Since we get all data into java, the performance is not good.

## SQL paging
We also can use paging of SQL. 	

Dao interface

	List<Student> queryStudentsBySql(Map<String,Object> data);

StudentMapper.xml

	<select id="queryStudentsBySql" parameterType="map" resultMap="studentmapper">
	    select * from student limit #{currIndex} , #{pageSize}
	</select>


Then we can get data we want:

    public List<Student> queryStudentsBySql(int currPage, int pageSize) {
        Map<String, Object> data = new HashedMap();
        data.put("currIndex", (currPage-1)*pageSize);
        data.put("pageSize", pageSize);
        return studentMapper.queryStudentsBySql(data);
    }

## interceptor
We also can use interceptor of mybatis to page.

	@Intercepts({@Signature(type = StatementHandler.class, method = "prepare", args = {Connection.class, Integer.class})})
	public class MyPageInterceptor implements Interceptor {
	
	    private int pageSize;
	    private int currPage;
	
	    private String dbType;
	
	    @Override
	    public Object intercept(Invocation invocation) throws Throwable {
	        StatementHandler statementHandler = (StatementHandler) invocation.getTarget();
	        MetaObject MetaObjectHandler = SystemMetaObject.forObject(statementHandler);
	
	        while (MetaObjectHandler.hasGetter("h")) {
	            Object obj = MetaObjectHandler.getValue("h");
	            MetaObjectHandler = SystemMetaObject.forObject(obj);
	        }
	
	        while (MetaObjectHandler.hasGetter("target")) {
	            Object obj = MetaObjectHandler.getValue("target");
	            MetaObjectHandler = SystemMetaObject.forObject(obj);
	        }
	        MappedStatement mappedStatement = (MappedStatement) MetaObjectHandler.getValue("delegate.mappedStatement");
	        String mapId = mappedStatement.getId();
	        if (mapId.matches(".+ByPage$")) {
	            ParameterHandler parameterHandler = (ParameterHandler) MetaObjectHandler.getValue("delegate.parameterHandler");
	
	            Map<String, Object> paraObject = (Map<String, Object>) parameterHandler.getParameterObject();
	
	
	            currPage = (int) paraObject.get("currPage");
	            pageSize = (int) paraObject.get("pageSize");
	
	            String sql = (String) MetaObjectHandler.getValue("delegate.boundSql.sql");
	            String limitSql;
	            sql = sql.trim();
	            limitSql = sql + " limit " + (currPage - 1) * pageSize + "," + pageSize;
	            MetaObjectHandler.setValue("delegate.boundSql.sql", limitSql);
	        }
	        return invocation.proceed();
	    }
	
	    @Override
	    public Object plugin(Object o) {
	        return Plugin.wrap(o, this);
	    }
	
	    @Override
	    public void setProperties(Properties properties) {
	
	        String limit1 = properties.getProperty("limit", "10");
	        this.pageSize = Integer.valueOf(limit1);
	        this.dbType = properties.getProperty("dbType", "mysql");
	    }
	}

After finish writing the interceptor, we need to register it.

    <plugins>
        <plugin interceptor="com.kincolle.interceptor.MyPageInterceptor">
            <property name="limit" value="10"/>
            <property name="dbType" value="mysql"/>
        </plugin>
    </plugins>


Dao interface

	List<Student> queryStudentsByPage(Map<String,Object> data);

StudentMapper.xml
    
    <select id="queryStudentsByPage" parameterType="map" resultMap="studentmapper">
        select * from student
    </select>

Finally we use it

    public List<Student> queryStudentsByPage(int currPage, int pageSize) {
        Map<String, Object> data = new HashedMap();
        data.put("currPage", currPage);
        data.put("pageSize", pageSize);
        return studentMapper.queryStudentsByPage(data);
    }



