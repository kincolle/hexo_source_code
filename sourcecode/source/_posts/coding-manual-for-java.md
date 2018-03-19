---
title: coding manual for java
date: 2018-03-02 07:15:32
tags:
categories:
- Java Learning
- Rule learning
- Coding Rule
---
# Coding Rule

First of all, I have learning Coding Rule of Alibaba Group, so I write this blog to learn it again.

## 1 Naming

1. Name in code can not be starting or ending with _ or $ 
<label style="color:red">wrong example</label>: <br>  _name/ __name / $name / name_ / name$ / name__

2. Name must be in English 
<label style="color:blue">right example</label>:<br> alibaba / google /apple 
<label style="color:red">wrong example</label>:<br> guge / pingguo 

3. Name of Class must use UpperCamelCase style except DO / BO / DTO /VO and so on.
<label style="color:blue">right example</label>: <br>UserDO / TcpUdpDeal 
<label style="color:red">wrong example</label>: <br>userDO / tcpudpdeala

4. Name of function, parameter, class variable and local variable must use lowerCamelCase styple.
<label style="color:blue">right example</label>:<br> localValue

5. Name of Const variables need to be writed as upperclass and words are spletted by "_". You'd better  let the name to be easy to understand and not to be too long.
<label style="color:blue">right example</label>: <br>MAX_STOCK_COUNT
<label style="color:red">wrong example</label>: <br>MAX_COUNT

6. Name of abstract class need to start with Abstract or Base; Name of exception class neet to be end with Exception; Name of test class need to be end with Test 
<label style="color:blue">right example</label>: <br>MAX_STOCK_COUNT
<label style="color:red">wrong example</label>: <br>MAX_COUNT

7. Type and [] need to stay close to difine a array
<label style="color:blue">right example</label>: <br>int[] arrayDemo
<label style="color:red">wrong example</label>: <br>int arrayDemop[] 