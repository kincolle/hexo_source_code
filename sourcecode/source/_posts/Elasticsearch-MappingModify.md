---
title: Mapping Modify
date: 2018-06-03 09:31:21
tags:
categories:
- Elasticsearch
- Mapping Modify
---

First，if we have a mapping like the following:

>{
>&nbsp;&nbsp;&nbsp;&nbsp;	"settings": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		"number_of_shards": 20
>&nbsp;&nbsp;&nbsp;&nbsp;	},
>&nbsp;&nbsp;&nbsp;&nbsp;	"mappings": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		"client": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			"properties": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;				"ip": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;					"type": "long"
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;				},
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;				"cost": {
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;					"type": "long"
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;				}
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;			}
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		}
>&nbsp;&nbsp;&nbsp;&nbsp;	}
>}

Then if we want to change the mapping like the following:

	http://localhost:8301/store/client/_mapping

	{
	    "client" : {
	        "properties" : {
	            "local_ip" : {"type" : "string", "store" : "yes"}   
	        }
	    }
	}


The result will be
>{"error":"MergeMappingException[Merge failed with failures {[mapper [local_ip] of different type, current_type [long], merged_type [string]]}]","status":400}
 
So, we can see that created mapping field can not be modified but we can create new field Like the following:

	http://localhost:8301/index/_mapping/type


	{
	    "properties": {
	        "newField": {
	            "type": "keyword",
	            "store": true
	        }
	    }
	}


