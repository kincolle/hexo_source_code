---
title: UDTF
date: 2018-05-28 22:13:31
tags:
categories:
- Hive
- UDTF
---

## Introduction

UDTF, that is short for User-Defined Table-Generating Functions, can be created by extending the GenericUDTF abstract class and then implementing the initialize, process, and possibly close methods. The initialize method is used to define the argument types to expect. The UDTF must then return an object inspector corresponding to the row objects that the UDTF will generate. After calling  initialize(), using the process() method to give rows. While in process(), the UDTF can produce and forward rows to other operators by calling forward(). Lastly, Hive will call the close() method when all the rows have passed to the UDTF.

## Example
The content in studentScore.txt is like the following

|||
| :-: | :-: | :-: | 
|A|90|
|A|80|
|A|70|
|A|50|
|B|60|
|B|90|
|B|95|
|B|80|

##### (1) Upload studentScore.txt to HDFS
bin/hadoop fs -put /home/wangzhun/tmp/studentScore.txt wz

##### (2) Create table and input content of studentScore.txt

 	create table studentScore(name string,score int) ROW FORMAT DELIMITED Fields TERMINATED BY '\t';

    load data inpath '/user/root/wz/studentScore.txt' overwrite into table studentScore;

##### (3) Write UDTF program


	package com.wz.udf;  
	import java.util.ArrayList;  
	import org.apache.hadoop.hive.ql.udf.generic.GenericUDTF;  
	import org.apache.hadoop.hive.ql.exec.UDFArgumentException;  
	import org.apache.hadoop.hive.ql.exec.UDFArgumentLengthException;  
	import org.apache.hadoop.hive.ql.metadata.HiveException;  
	import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspector;  
	import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspectorFactory;  
	import org.apache.hadoop.hive.serde2.objectinspector.StructObjectInspector;  
	import org.apache.hadoop.hive.serde2.objectinspector.primitive.PrimitiveObjectInspectorFactory;  
	  
	public class staticScore extends GenericUDTF{  
	  Integer nTotalScore = Integer.valueOf(0);       //score  
	  Object forwardObj[] = new Object[1];  
	  String strStudent;       //student name  
	  @Override  
	  public void close() throws HiveException {    
	        //output score of the last student  
	    forwardObj[0]=(strStudent+":"+String.valueOf(nTotalScore));  
	    forward(forwardObj);  
	    }  
	  
	    
	  @Override  
	  public StructObjectInspector initialize(ObjectInspector[] args)throws UDFArgumentException {  
	          strStudent="";  
	      ArrayList<String> fieldNames = new ArrayList<String>();  
	          ArrayList<ObjectInspector> fieldOIs = new ArrayList<ObjectInspector>();  
	          fieldNames.add("col1");  
	          fieldOIs.add(PrimitiveObjectInspectorFactory.javaStringObjectInspector);  
	          return ObjectInspectorFactory.getStandardStructObjectInspector(fieldNames,fieldOIs);  
	       }  
	  
	  
	  @Override  
	  public void process(Object[] args) throws HiveException {  
	    if(!strStudent.isEmpty() && !strStudent.equals(args[0].toString()))  
	    {  
	       String[] newRes = new String[1];  
	       newRes[0]=(strStudent+":"+String.valueOf(nTotalScore));  
	       forward(newRes);  
	       nTotalScore=0;  
	    }     
	    strStudent=args[0].toString();  
	    nTotalScore+=Integer.parseInt(args[1].toString());  
	       }  
	   }  
             
##### (4) Package jar

	javac -classpath /home/wangzhun/hadoop/hadoop-0.20.2/hadoop-0.20.2-core.jar:/home/wangzhun/hive/hive-0.8.1/lib/hive-exec-0.8.1.jar staticScore.java

    jar -cvf staticScore.jar com/wz/udf/staticScore.class

##### (5) upload the jar into hive and do computing



    add jar /home/wangzhun/hive/hive-0.8.1/lib/staticScore.jar;
 
	create temporary function statics as 'com.wz.udf.staticScore';

Then the result will be:
 
	hive> select statics(studentScore.name,studentScore.score) as col1 from studentScore;  
	
	A:290  
	B:325     