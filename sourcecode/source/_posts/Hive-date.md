---
title: date
date: 2018-05-30 8:35:19
tags:
categories:
- Hive
- date
---

## 1. Get today's date
Code is 
	
	select from_unixtime(unix_timestamp(),'yyyy-MM-dd HH:mm:ss')

The result will be

	2016-09-14 16:21:59

## 2. get yesterday and tomorrow
We can use UDF to get the time, here is an example:
	
	public class NextDay extends UDF {
	    public String evaluate(int i) {
	        Date date=new Date();//Get time
	        Calendar calendar = new GregorianCalendar();
	        calendar.setTime(date);
	        calendar.add(calendar.DATE,i);//0 is today
	        date=calendar.getTime();      
	        SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
	        String dateString = formatter.format(date);
	        return dateString;
	    }
	    public static void main(String[] args) {
	        System.out.println("today:"+new NextDay().evaluate(0));
	        System.out.println("tomorrow:"+new NextDay().evaluate(1));
	        System.out.println("the day before yesterday:"+new NextDay().evaluate(-1));
	    }
	}

