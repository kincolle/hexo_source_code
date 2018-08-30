---
title: paging
date: 2018-06-16 23:32:49
tags:
categories:
- Hive
- paging
---

## Introduction
In hive there is no "limit start,end" function like mysql, so for paging some other ways must be taken.

## 1. use unique field
If in table there has a unique field, you can use it for paging: 

get data of the first page:
	
	select * from table order by id asc limit 10;

get data of the second page:

	select * from table where id >preId order by id asc limit 10;

## 2. use row_number()
For table without any unique field, you can use row_number() function: 

	select * from (select row_number() over (order by xx) as rnum ,table.* from table)t where rnum betwneen 1 to 10;