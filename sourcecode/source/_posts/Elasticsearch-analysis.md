---
title: analysis
date: 2018-06-04 19:21:31
tags:
categories:
- Elasticsearch
- analysis
---

When we create a index, we can set its analysis liek the following:

	PUT /my_index
	{
	    "settings": {
	        "analysis": {
	            "char_filter": { ... custom character filters ... },
	            "tokenizer":   { ...    custom tokenizers     ... },
	            "filter":      { ...   custom token filters   ... },
	            "analyzer":    { ...    custom analyzers      ... }
	        }
	    }
	}

For example, if we set a filter like

	"filter": {
	    "dot": {
	        "type":        "custom",
	        "stopwords": [ "," ]
	    }
	}

The we can analysis the ",". 