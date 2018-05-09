---
title: Straight Insertion Sort
date: 2018-03-29 20:29:16
tags:
categories:
- Algorithm
- Sort
- Straight Insertion Sort
---
## Introduction

Insert one value into a sorted table, then get a new table which has increased one value in order. It is stable and will take O(n<sup>2</sup>).

Like the following picture shows:

![](Algorithm-Sort-StraightInsertionSort/insertionsort.png)

## Code
The java example is:

	public class StraightInsertionSort {
	    public static void main(String[] args) {
	        int a[] = {3,1,5,7,2,4,9,6,10,8};
	        StraightInsertionSort obj=new StraightInsertionSort();
	        System.out.println("Initial value：");
	        obj.print(a);
	        obj.insertSort(a);
	        System.out.println("\nAfter sort：");
	        obj.print(a); 
	    }
	    public void print(int a[]){
	        for(int i=0;i<a.length;i++){
	            System.out.print(a[i]+" "); 
	        } 
	    }
	    public void insertSort(int[] a) {
	        for(int i=1;i<a.length;i++){
	            int j;
	            int x=a[i];//x is the value need to be inserted
	            for( j=i;  j>0 && x<a[j-1];j--){
	                a[j]=a[j-1]; 
	            }
	            a[j]=x;//insert
	        } 
	    }
	}
 
The result will be:

	Initial value：
	3 1 5 7 2 4 9 6 10 8 
	After sort：
	1 2 3 4 5 6 7 8 9 10