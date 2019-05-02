---
title: Vector Space Model
date: 2018-10-25 19:39:13
tags:
categories:
- Lucene
- Scoring
- Vector Space Model
---
# Vector Space Model

In Vector Space Model, here is a document that D=D(t<sub>1</sub>w<sub>2</sub>;t<sub>2</sub>w<sub>1</sub>;...;t<sub>n</sub>w<sub>n</sub>), and there are 2 assumption:

(1) each term has no relationship

(2) there is no order for every term.



So, we can have a N dimension space for a document like the following:

![](Lucene-Scoring-VectorSpaceModel/vsm.png)

## Cosine similarity
For 2 documents D1 and D2, their similarity Sim(D1,D2) can be computed like the following:

(1) We can 2 VSM for D1 and D2


D1=D(w<sub>11</sub>;w<sub>12</sub>;...;w<sub>1n</sub>)

D2=D(w<sub>21</sub>;w<sub>22</sub>;...;w<sub>2n</sub>)

(2) Then we can computing the similarity

![](Lucene-Scoring-VectorSpaceModel/1.png)

(3) Also, we can use the COS

![](Lucene-Scoring-VectorSpaceModel/2.png)
