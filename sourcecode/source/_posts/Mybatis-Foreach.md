---
title: foreach
date: 2018-08-21 23:02:19
tags:
categories:
- Mybatis
- foreach
---

### Introduction
The foreach is always used in the in contition. In the foreach there has 5 elements: item, index, collection, open, separator and close.

- item: define the name of element which will be iterated
- index: define the name of positoin where the iteration it is
- open: what will be started with
- separator: what symbol will be used for separation
- close: what will be end with

There are 3 types of collection when use in:

1. The collection is a list
2. The collection is an array
3. The collection is a map

## List
Here we have a mapper which the collection is a list.

	<select id="dynamicForeachTest" parameterType="java.util.List" resultType="Blog">
           select * from t_blog where id in
        <foreach collection="list" index="index" item="item" open="(" separator="," close=")">
                #{item}       
        </foreach>    
    </select>

The interface is：

	public List dynamicForeachTest(List ids); 

Then the test code is:

	 @Test
     public void dynamicForeachTest() {
         SqlSession session = Util.getSqlSessionFactory().openSession();      
         BlogMapper blogMapper = session.getMapper(BlogMapper.class);
         List ids = new ArrayList();
         ids.add(1);
         ids.add(3);
         ids.add(6);
         List blogs = blogMapper.dynamicForeachTest(ids);
          for (Blog blog : blogs)
              System.out.println(blog);
          session.close();
     }

## Array
Here we have a mapper which the collection is an array.

	<select id="dynamicForeach2Test" parameterType="java.util.ArrayList" resultType="Blog">
	    select * from t_blog where id in
	    <foreach collection="array" index="index" item="item" open="(" separator="," close=")">
	         #{item}
	    </foreach>
	</select>    

The interface is：

	public List dynamicForeach2Test(int[] ids); 

Then the test code is:
	
	@Test
	public void dynamicForeach2Test() {
	      SqlSession session = Util.getSqlSessionFactory().openSession();
	        BlogMapper blogMapper = session.getMapper(BlogMapper.class);
	        int[] ids = new int[] {1,3,6,9};
	        List blogs = blogMapper.dynamicForeach2Test(ids);
	        for (Blog blog : blogs)
	        System.out.println(blog);    
	         session.close();
	}

## Map
Here we have a mapper which the collection is a map.

	<select id="dynamicForeach3Test" parameterType="java.util.HashMap" resultType="Blog">
	        select * from t_blog where title like "%"#{title}"%" and id in
	        <foreach collection="ids" index="index" item="item" open="(" separator="," close=")">
	             #{item}
	        </foreach>
	</select>

The interface is：

	public List dynamicForeach3Test(Map params); 

Then the test code is:

	@Test
    public void dynamicForeach3Test() {
        SqlSession session = Util.getSqlSessionFactory().openSession();
        BlogMapper blogMapper = session.getMapper(BlogMapper.class);
        final List ids = new ArrayList();
        ids.add(1);
        ids.add(2);
        ids.add(3);
        ids.add(6);
        ids.add(7);
        ids.add(9);
        Map params = new HashMap();
        params.put("ids", ids);
        params.put("title", "中国");
        List blogs = blogMapper.dynamicForeach3Test(params);
        for (Blog blog : blogs)
            System.out.println(blog);
        session.close();
     } 