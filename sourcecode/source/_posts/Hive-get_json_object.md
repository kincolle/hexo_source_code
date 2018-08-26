---
title: get_json_object
date: 2018-05-29 20:23:41
tags:
categories:
- Hive
- get_json_object
---

## Introduction

The get_json_object is a hive function used for deserializing json.

## Example
Here we have a json like this

> {
>&nbsp;&nbsp;&nbsp;&nbsp;	"store": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		"fruit": [{
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			"weight": 1,
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			"type": "apple"
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		}, {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			"weight": 9,
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			"type": "pear"
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		}],
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		"bicycle": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			"price": 20,
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			"color": "red"
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		}
>&nbsp;&nbsp;&nbsp;&nbsp;	},
>&nbsp;&nbsp;&nbsp;&nbsp;	"email": "amy@only_for_json_udf_test.net",
>&nbsp;&nbsp;&nbsp;&nbsp;	"owner": "kincolle"
>}

We can get contents of json like the following:

	hive> SELECT get_json_object(src_json.json, '$.owner') FROM src_json;
	kincolle
	hive> SELECT get_json_object(src_json.json,'$.store.fruit[0]') FROM src_json;
	{"weight":1,"type":"apple"}
	hive> SELECT get_json_object(src_json.json,'$.non_exist_key') FROM src_json;
	NULL      