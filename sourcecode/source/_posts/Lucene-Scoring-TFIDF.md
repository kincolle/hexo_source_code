---
title: TF IDF
date: 2018-10-29 22:53:18
tags:
categories:
- Lucene
- Scoring
- TF IDF
---

## TF
In a given document, TF means a frequency of a term in the document.

![](Lucene-Scoring-TFIDF/1.png)

So it can be described like the following:

![](Lucene-Scoring-TFIDF/2.png)

##IDF
IDF means how important a term is. is can be computed by using number of a term to be divided by the number of the documents.

![](Lucene-Scoring-TFIDF/3.png)

So it can be described like the following:

![](Lucene-Scoring-TFIDF/4.png)

Finally, it is:TF-IDF(t) = TF(t) * IDF(t).