---
title: ResultMap And ResultType
date: 2018-06-14 23:28:39
tags:
categories:
- Mybatis
- ResultMap And ResultType
---

## ResultMap And ResultType
About ResultMap and ResultType, both of them are used in Mybatis. When we write mapper.xml, we always use them. So, let's analyze them.

Both of them are used for mapping search result with java instence.

##### ResultMap
It will map column into every variable in a java instence according to 'resultMap' which is defined by programmer. And there are some other label like: 

- id: define a unique key
- column: column name in result set
- property：Mapping virable into java instance
  
##### ResultType
The difference from ResultMap is that the name of column and of java instance must be the same.