---
title: List Sort
date: 2018-05-08 09:23:12
tags:
categories:
- Java Learning
- Coding
- List Sort
---
If you want to sort a list especially when you want to sort by using elements of collections. At this time you can use sort function of Collections class.

First, let's see a simple example:

	public static void main(String[] args) {  
	    List<Integer> nums = new ArrayList<Integer>();  
	        nums.add(3);  
	        nums.add(5);  
	        nums.add(1);  
	        nums.add(0);  
	        System.out.println(nums);  
	        Collections.sort(nums);  
	        System.out.println(nums);  
	}  

The result will be:

	[3, 5, 1, 0]
	[0, 1, 3, 5]

Then, let's see a complex example:

	public class User implements Comparable<User>{        
	    private int score;        
	    private int age;  
	      
	    public User(int score, int age){  
	        super();  
	        this.score = score;  
	        this.age = age;  
	    }  
	  
	    public int getScore() {  
	        return score;  
	    }  
	  
	    public void setScore(int score) {  
	        this.score = score;  
	    }  
	  
	    public int getAge() {  
	        return age;  
	    }  
	  
	    public void setAge(int age) {  
	        this.age = age;  
	    }  
	  
	    @Override  
	    public int compareTo(User o) {  
	        int i = this.getAge() - o.getAge();//order by age firstly  
	        if(i == 0){  
	            return this.score - o.getScore();//then order by socre  
	        }  
	        return i;  
	    }  
	}  
	  
	public static void main(String[] args) {  
	        List<User> users = new ArrayList<User>();  
	        users.add(new User(78, 26));  
	        users.add(new User(67, 23));  
	        users.add(new User(34, 56));  
	        users.add(new User(55, 23));  
	        Collections.sort(users);  
	        for(User user : users){  
	            System.out.println(user.getScore() + "," + user.getAge());  
	        }  
	}

Then the result will be
  
	55,23
	67,23
	78,26
	34,56