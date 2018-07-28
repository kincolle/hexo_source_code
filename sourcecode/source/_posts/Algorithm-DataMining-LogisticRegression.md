---
title: Logistic Regression
date: 2018-05-07 22:09:15
tags:
categories:
- Algorithm
- DataMining
- Logistic Regression
---
## 1. Introduction of Logistic Regression
Logistic Regression is a regression model where the dependent variable (DV) is categorical. This article covers the case of a binary dependent variable—that is, where the output can take only two values, "0" and "1". So, it can be used for clissification.

### Prediction Function
Since Logistic Regression is a clissification function, so we can use Sigmoid function to get it: 

![](Algorithm-DataMining-LogisticRegression/1.jpg)

This sigmoid functino has a "S" type line like the following picture:

![](Algorithm-DataMining-LogisticRegression/2.jpg)

In the following, in the left picture shows a line-board and in the right picture shows a none line-board.
 
![](Algorithm-DataMining-LogisticRegression/3.jpg)

For line-board, it is like the following picture:

![](Algorithm-DataMining-LogisticRegression/4.jpg)

So the Prediction Function is 

![](Algorithm-DataMining-LogisticRegression/5.jpg)

Function h<sub>θ</sub>(x) has a meaning that ratio of 1. 

![](Algorithm-DataMining-LogisticRegression/6.jpg) 

### Loss Funtion J
Cost function and J function is like the following:

![](Algorithm-DataMining-LogisticRegression/7.jpg) 
![](Algorithm-DataMining-LogisticRegression/8.jpg) 

Here is how it comes:

(1) equation can be writen as: 

![](Algorithm-DataMining-LogisticRegression/9.jpg)

Get its Likelihood function:

![](Algorithm-DataMining-LogisticRegression/10.jpg)

Logarithm of Likelihood function is:

![](Algorithm-DataMining-LogisticRegression/1.png) 

Then we get the largest θ
