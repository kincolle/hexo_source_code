---
title: Prefix Suffix
date: 2018-09-29 22:07:09
tags:
categories:
- Lucene
- Optimize Algorithm
- Prefix Suffix
---

# Prefix Suffix
In the index of the lucene, information of term dictionary is stored. Every term in the dictionary is sorted. But, some of terms are too long so that they will take a lot of space. And that will make the index file too large. The Prefix Suffix means that if one term shared a common prefix part or suffix part of another one, will can use an offset from the another one term instead of storing a whole term like the following     

![](Lucene-OptimizeAlgorithm-PrefixSuffix/prefixsuffix.jpg)

### prefixsuffix
For example, we have the following term:

	term, termagancy, termagant, terminal

If we store every whole term, the needed space will be like:

***
[VInt = 4] [t][e][r][m]，[VInt = 10][t][e][r][m][a][g][a][n][c][y]，[VInt = 9][t][e][r][m][a][g][a][n][t]，[VInt = 8][t][e][r][m][i][n][a][l]
***

It will cost 35 Bytes. 

By using the Prefix Suffix, the need space will be like:

***
[VInt = 4] [t][e][r][m]，[VInt = 4 (offset)][VInt = 6][a][g][a][n][c][y]，[VInt = 8 (offset)][VInt = 1][t]，[VInt = 4(offset)][VInt = 4][i][n][a][l]
***

It will cost 22 Bytes. Absolutely we shrink the used space successfully.