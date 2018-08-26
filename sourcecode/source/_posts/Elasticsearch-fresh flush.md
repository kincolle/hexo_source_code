---
title: fresh and flush
date: 2018-06-05 21:31:46
tags:
categories:
- Elasticsearch
- fresh and flush
---

## (1) fresh and flush
When index a document, the document is stored in the main memory in the first time. Then the fresh is used for writing the data into the harddisk or then data may lost.

## (2) flush
The flush is used for translog.
For data safety, when we do index a document, a translog will be written for data safety. The flush will call lucene commit and clear all translog files. The flush of translog operates automatically and will check translog every 5 seconds.


 