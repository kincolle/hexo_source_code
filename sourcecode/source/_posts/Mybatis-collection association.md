---
title: Collection And Association
date: 2018-06-23 23:11:52
tags:
categories:
- Mybatis
- Collection And Association
---

## One Mapping One
##### 1. Association
association is always used for one mapping one relationship.Here is an example:

Here we have a User entity class:

    private String id;//keyid
    private String userName;//name of user

Then we have a Article entity class:
    
	private String id;//keyid
    private String articleTitle;//title of article
    private String articleContent;//content of article

If we want to search a user and one of his article at the same time, how should we do? You may add a field in User class.
	
	private String id;//keyid
    private String userName;//name of user
    private Article article;//new field abtou article

##### 2.mapper.xml
We write mapper.xml of user class like the following.

	<resultMap id="userResultMap" type="test.mybatis.entity.User">
		<id column="id" property="id" jdbcType="VARCHAR" javaType="java.lang.String"/>
		<result column="userName" property="userName" jdbcType="VARCHAR" javaType="java.lang.String"/>
		<association property="article" column="id" select="test.mybatis.dao.articleMapper.selectArticleByUserId" />
	</resultMap>

mapper.xml of article class will like the following.

	<resultMap id="articleResultMap" type="test.mybatis.entity.Article">
		<id column="id" property="id" jdbcType="VARCHAR" javaType="java.lang.String"/>
		<result column="articleTitle" property="articleTitle" jdbcType="VARCHAR" javaType="java.lang.String"/>
		<result column="articleContent" property="articleContent" jdbcType="VARCHAR" javaType="java.lang.String"/>
	</resultMap>
	<select id="selectArticleByUserId" parameterType="java.lang.String" resultMap="ArticleResultMap">
		select * from tb_article where userId=#{userId} 
	</select>

## One Mapping More
##### collection 
We have a entity like the following:

	private String id;//keyid
    private String userName;//name of user
    private List<Article> articleList;//article list

userMapper.xml will be like:

	<resultMap id="userResultMap" type="test.mybatis.entity.User">
		<id column="id" property="id" jdbcType="VARCHAR" javaType="java.lang.String"/>
		<result column="userName" property="userName" jdbcType="VARCHAR" javaType="java.lang.String"/>
	    <collection property="articleList" column="id" select="test.mybatis.dao.articleMapper.selectArticleListByUserId" />
 	</resultMap>
	
