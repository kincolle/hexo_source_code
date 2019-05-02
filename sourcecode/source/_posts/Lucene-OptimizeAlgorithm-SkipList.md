---
title: Skip List
date: 2018-10-17 23:03:49
tags:
categories:
- Lucene
- Optimize Algorithm
- Skip List
---
# Skip List
Skip lists are a probabilistic data structure that seem likely to supplant balanced trees as the implementation method of choice for many applications. Skip list algorithms have the same asymptotic expected time bounds as balanced trees and are simpler, faster and use less space. 

![](Lucene-OptimizeAlgorithm-SkipList/SkipList.png)

The probability of an element of ith layout show up at (i+1)th layout is P which is const. Usually, every element in 1/(1-p) linkedlists.