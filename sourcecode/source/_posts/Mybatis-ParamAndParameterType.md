---
title: parametertype
date: 2018-06-08 23:21:42
tags:
categories:
- Mybatis
- parametertype
---

## Parametertype
In mybatis, there are some ways to input param into your sql. 

## (1) parameterType
In mybatis you can use parameterType to input param.

- Base data: like int, String and Date
- Complex data: like Class, Map.

Here are three examples of searching teacher list according class id:

##### Base data  
XML file:

	<select id="selectTeacher" parameterType="int" resultType="kincolle.domain.Teacher">  
	    select * from Teacher where c_id=#{id}  
	</select>  

Java code:

	List<Teacher> tList = teacherMapper.selectTeacher(1);    
	for (Teacher entityTemp : tList) {    
	    System.out.println(entityTemp.toString());    
	} 

##### Java Class
XML file:

	<select id="selectTeacher" parameterType="kincolle.domain.Teacher" resultType="kincolle.domain.Teacher">  
	    select * from Teacher where c_id=#{id}  
	</select>  

Java code:

	Teacher queryTeacher=new Teacher();  
	queryTeacher.setId(1);  
	List<Teacher> tList = teacherMapper.selectTeacher(queryTeacher);    
	for (Teacher entityTemp : tList) {    
	    System.out.println(entityTemp.toString()); 
	} 

##### Map
XML file:

	<select id="selectTeacher" parameterType="Map" resultType="kincolle.domain.Teacher">  
	    select * from Teacher where c_id=#{id} and sex=#{sex}  
	</select>  

Java code

	Map<String,String> map=new HasMap<String,String>();  
	map.put("id","1");  
	map.put("sex","male");  
	List<Teacher> tList = teacherMapper.selectTeacher(map);    
	for (Teacher entityTemp : tList) {    
	    System.out.println(entityTemp.toString()); 
	}  

## (2) @param
Another way is using the @param.

In interface:

	public List<Teacher> selectTeacher(@Param(value="id") String id,@Param(value="sex") String sex);

XML file:

	<select id="selectTeacher"  resultType="kincolle.domain.Teacher">  
	    select * from Teacher where c_id=#{id} and sex=#{sex}  
	</select>

Java code:

	List<Teacher> tList = teacherMapper.selectTeacher("1","male");    
	for (Teacher entityTemp : tList) {    
	    System.out.println(entityTemp.toString());
	}