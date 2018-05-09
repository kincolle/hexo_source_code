---
title: Binary Tree
date: 2018-04-29 11:35:23
tags:
categories:
- Algorithm
- DataStructure
- Binary Tree
---
## 1. Definition of Tree
Tree is a kind of data structure and it is constructed by n(n>=1) nodes to form a relationship with some layouts.

![](Algorithm-DataStructure-BinaryTree/1.png)

There are some characters in Tree:

- every node has 0 or more than 0 node
- node with no father node is the root node
- every node except the root node has a father node
- every node excepy the root node can divide into more than one subtree.

## 2. Binary Tree
### 2.1 Definition of Binary Tree
Binary tree is a kind of tree than its number of subtree is less than 2 for every node.

![](Algorithm-DataStructure-BinaryTree/2.png)

### 2.2 Character of Binary Tree
- The largest number at the i<sub>th</sub> layout is 2<sup>i-1</sup>(i>=1)
- Binary tree that its height is k has less than  2<sup>k</sup>-1 nodes
- Binary tree that has n nodes has a height at least (log<sub>2</sub>n)+1

## 3. Full Binary Tree
A binary tree that its weight is h and number of its node is 2h-1,then it is a Full Binary Tree 

![](Algorithm-DataStructure-BinaryTree/3.png)

## 4. Complete Binary Tree
A Binary Tree is complete Binary Tree if all levels are completely filled except possibly the last level and the last level has all keys as left as possible. 

![](Algorithm-DataStructure-BinaryTree/4.png)