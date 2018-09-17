---
title: Util
date: 2018-08-02 21:01:08
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Util
---

Here I will give you an example of the spring util label.
 
## applicationContext.xml

	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:c="http://www.springframework.org/schema/c"
	       xmlns:util="http://www.springframework.org/schema/util"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	                        http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd ">
	
	    <bean id="blankDisc" class="com.kincolle.BlankDisc" c:_0="aaaa" c:_1="111"/>
	    <bean id="person" class="com.hk.Person">
	        <property name="list" ref="list"/>
	        <property name="map" ref="map"/>
	        <property name="set" ref="set"/>
	    </bean>
	
	    <util:list id="list">
	        <value>listValue1111</value>
	        <value>listValue2222</value>
	        <value>listValue3333</value>
	    </util:list>
	
	    <util:map id="map">
	        <entry key="map1" value="11111"/>
	        <entry key="map2" value="22222"/>
	        <entry key="map3" value="33333"/>
	    </util:map>
	
	    <util:set id="set">
	        <value>setValue1</value>
	        <value>setValue2</value>
	        <value>setValue3</value>
	    </util:set>
	</beans>


## JavaBean

	public class Person {
	
	    private List list;
	    private Set set;
	    private Map map;
		
	    public void setList(List list) {
	        this.list = list;
	    }
	
	    public void setSet(Set set) {
	        this.set = set;
	    }
	
	    public void setMap(Map map) {
	        this.map = map;
	    }
	
	
	    public List getList() {
	        return list;
	    }
	
	    public Set getSet() {
	        return set;
	    }
	
	    public Map getMap() {
	        return map;
	    }
	}

## Test

	public class App 
	{
	    @Autowired
	    private static CompactDisc compactDisc;
	
	    public static void main( String[] args )
	    {
	        ApplicationContext application = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
	        CompactDisc c = (CompactDisc) application.getBean("blankDisc");
	        c.play();
	        Person p = (Person) application.getBean("person");
	        List list = p.getList();
	        for (int i = 0; i < list.size(); i++) {
	            String aa = (String) list.get(i);
	            System.out.println(aa);
	        }
	
	    }
	
	
	    private void play(){
	        compactDisc.play();
	    }
	}

## Util Type

Util label | describe 
- | -  
&lt; util:constant &gt; | point to a public static field and let it to be a bean 
&lt; util:list &gt; | create java.util.list type as a bean 
&lt; util:map &gt; | create java.util.map type as a bean
&lt; util:properties &gt; | create java.util.properties type as a bean
&lt; util:property-path &gt; | point to a variable as a bean
&lt; util:set &gt; | create java.util.set type as a bean
