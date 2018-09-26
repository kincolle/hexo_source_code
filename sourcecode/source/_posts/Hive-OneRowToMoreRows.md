---
title: One Row To More Rows
date: 2018-08-19 16:33:18
tags:
categories:
- Hive
- One Row To More Rows
---

## Introduction
if we have data like the following:

| name | alias| 
| ------ | ------ | 
| kincolle | abc def ghi|

and we want to get a table like the following:

| name | alias| 
| ------ | ------ | 
| kincolle | abc |
| kincolle | def |
| kincolle | ghi|

The way to do it is using "Lateral view" and "explode()". Here is the example:

	hive> select name, single_alias from test lateral view explode(split(alias, ' ')) test_alias as single_alias;

The explode() is used for let array data into a single row data, like explode([1, 2, 3]) will be
  
- 1
- 2
- 3


