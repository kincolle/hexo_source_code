---
title: Deserialization Feature
date: 2018-05-10 19:23:12
tags:
categories:
- Java Learning
- Jackson
- Deserialization Feature
---

# Introduction

Feature that determines whether encountering of unknown properties (ones that do not map to a property, and there is no "any setter" or handler that can handle it) should result in a failure (by throwing a JsonMappingException) or not. This setting only takes effect after all other handling methods for unknown properties have been tried, and property remains unhandled.
Feature is enabled by default (meaning that a JsonMappingException will be thrown if an unknown property is encountered).

# Example

There is a json string like 

	{"id":null,"username":"hellow","passwordXXXX":"123456"}	

Then you want to deserialize a User like


	public class User {
		private Long id;
		private String username;
		private String password;
	}

Accoding to the string, there is a varible named passwordXXXX which is not in the User class. If you do deserialization you will get a JsonMappingException. But if you set DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES as flase, the exception will not come. Like the following:


    public static <T> T parse(String json, Class<T> clazz,boolean failOnUnknownProperties){
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, failOnUnknownProperties);
        T result=null;
        try {
            result=objectMapper.readValue(json, clazz);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return result;
    }

  
