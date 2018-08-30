---
title: Dynamic SQL
date: 2018-07-02 23:15:23
tags:
categories:
- Mybatis
- Dynamic SQL
---

## Introduction
In the traditional function of JDBC, we need to write a complex SQL by ourself. To slove those problems, we can use Dynamic SQL of mybatis like if, choose, when, otherwise, trim, where, set, foreach.   

## if

	<select id="selectUserByUsernameAndSex" resultType="user" parameterType="kincolle.User">
    	select * from user where username=#{username} and sex=#{sex}
	</select>

In the example if  #{username} is  null then result will be null. To solve this problem we use if.

	<select id="selectUserByUsernameAndSex" resultType="user" parameterType="kincolle.User">
	    select * from user where
	        <if test="username != null">
	           username=#{username}
	        </if>
	         
	        <if test="username != null">
	           and sex=#{sex}
	        </if>
	</select> 

Then we can see if sex is null then the SQL will be "select * from user where username=#{username}". But if username is null the SQL will be "select * from user where and sex=#{sex}". definitely it's wrong. to Solve this problem we can use where.

## if+where
To solve the problem in the example of if, we use where.

	<select id="selectUserByUsernameAndSex" resultType="user" parameterType="kincolle.User">
	    select * from user
	    <where>
	        <if test="username != null">
	           username=#{username}
	        </if>
	         
	        <if test="username != null">
	           and sex=#{sex}
	        </if>
	    </where>
	</select>

If we use where and if "AND" or "OR" is in head it will be deleted.

## if+set

If we have a set, then we can do like the following.

	<update id="updateUserById" parameterType="kincolle.User">
	    update user u
	        <set>
	            <if test="username != null and username != ''">
	                u.username = #{username},
	            </if>
	            <if test="sex != null and sex != ''">
	                u.sex = #{sex}
	            </if>
	        </set>
	     
	     where id=#{id}
	</update>

If username is null then the SQL will be "update user u set u.sex=? where id=?". If not, the SQl will be "update user u set u.username = ? ,u.sex = ? where id=?".

## choose(when,otherwise)

Sometimes, we do not want to use all conditions but just one of them then we can use choose:

	<select id="selectUserByChoose" resultType="kincolle.User" parameterType="kincolle.User">
      select * from user
      <where>
          <choose>
              <when test="id !='' and id != null">
                  id=#{id}
              </when>
              <when test="username !='' and username != null">
                  and username=#{username}
              </when>
              <otherwise>
                  and sex=#{sex}
              </otherwise>
          </choose>
      </where>
    </select>  


If id is not null then the SQL is "select * from user where  id=?". If id is null and username is not null then the SQL is "select * from user where  username=?". If both id and username is null then the SQL is "select * from user where sex=?".

## trim
The trim is a format label, it can do function of set or where. 
##### 1. if+where
	<select id="selectUserByUsernameAndSex" resultType="user" parameterType="kincolle.User">
        select * from user
        <!-- <where>
            <if test="username != null">
               username=#{username}
            </if>
             
            <if test="username != null">
               and sex=#{sex}
            </if>
        </where>  -->
        <trim prefix="where" prefixOverrides="and | or">
            <if test="username != null">
               and username=#{username}
            </if>
            <if test="sex != null">
               and sex=#{sex}
            </if>
        </trim>
    </select>


##### 2. if+set 

    <update id="updateUserById" parameterType="kincolle.User">
        update user u
            <!-- <set>
                <if test="username != null and username != ''">
                    u.username = #{username},
                </if>
                <if test="sex != null and sex != ''">
                    u.sex = #{sex}
                </if>
            </set> -->
            <trim prefix="set" suffixOverrides=",">
                <if test="username != null and username != ''">
                    u.username = #{username},
                </if>
                <if test="sex != null and sex != ''">
                    u.sex = #{sex},
                </if>
            </trim>
         
         where id=#{id}
    </update>

## foreach
If in user table we can 3 users that 1, 2 and 3. We can use SQL like "select * from user where id=1 or id=2 or id=3" or "select * from user where id in (1,2,3)".

(1)  build a UserVo class which has "List<Integer> ids"  

	package kincolle.vo;
	
	import java.util.List;
	
	public class UserVo {

	    private List<Integer> ids;
	 
	    public List<Integer> getIds() {
	        return ids;
	    }
	 
	    public void setIds(List<Integer> ids) {
	        this.ids = ids;
	    }	 
	}　


(2) use foreach
	
	<select id="selectUserByListId" parameterType="com.ys.vo.UserVo" resultType="kincolle.User">
	    select * from user
	    <where>
	        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
	            id=#{id}
	        </foreach>
	    </where>
	</select>

then do the test:

	@Test
	public void testSelectUserByListId(){
	    String statement = "kincolle.userMapper.selectUserByListId";
	    UserVo uv = new UserVo();
	    List<Integer> ids = new ArrayList<>();
	    ids.add(1);
	    ids.add(2);
	    ids.add(3);
	    uv.setIds(ids);
	    List<User> listUser = session.selectList(statement, uv);
	    for(User u : listUser){
	        System.out.println(u);
	    }
	    session.close();
	}