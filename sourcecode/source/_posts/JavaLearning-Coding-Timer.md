---
title: Timer
date: 2018-08-21 23:02:19
tags:
categories:
- Java Learning
- Coding
- Timer
---

Here ia an example:

	public class TestTimer {
	    public static void main(String[] args) {
	        Timer timer = new Timer();
	        Task task = new Task();
	        timer.schedule(task, new Date(), 1000);
	    }
	}
	
	class Task extends TimerTask{
	
	    @Override
	    public void run() {
	        System.out.println("Do work...");
	    }
	}

The result will be:

	Do work...
	Do work...
	Do work...
	Do work...