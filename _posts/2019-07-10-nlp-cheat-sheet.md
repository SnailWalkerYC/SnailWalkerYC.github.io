---
layout: post
title: Search Engine Cheat Sheet
date: 2019-07-10
Author: 来自中世界
categories: [technology]
tags: [search]
comments: true
---

# Search Engine Cheat Sheet 



All following knowledge is conclusion from [Jamie Callan](https://boston.lti.cs.cmu.edu/classes/11-642/). By the way, he is the most friendly and helpful professor I have met. 



## Basic Rules 

**Bag of word model:** ignore word order, discard some meaningless words like "the", transfrom words into terms. 

**Heap's law** predict the number of vocabulary size. 

**Zipf's law:** the ratio of the item in the word collection is relative to its ranking. A/R. 

Term frequency: number of term appear in one document (tf)

Document frequency: the number of documents contain a spedific term (df)

Search engine is to satisfy the information need (with query) with a corpus of documents (with structured documents)

## Query Operator 

- "AND", "OR", "NEAR/n".

- Query can be represented as tree. Like (tomorrow OR today) AND rain. Most of the time the query is represented as prefix representation like cat AND dog is AND (cat dog)
- document can be represented as free-text indexing and full-text indexing. And every term can be represented as inverted lists. The list of document is a vector which is sparse. So the inverted list is used(binay, frequency, positional). Inverted index consists of inverted lists and access mechanism.  (B-Tree and hash table). 
- Ranked boolean and unranked boolean: wetather the term frequency is considered or not. In ranked boolean, tf*idf is used, term frequency and inverted document frequency. Log(N/df). 
- Ranked method is hard to get good balance of precision and recall. 
- Query processing order: TAAT(Term-at-a-time, memory will not suitable) and DAAT(document at one time, more complex implementation like nested loops, using recursive tree).  
- Query parsing: QryEval is excellent abstract class, the heritage class can be operation subclass or term subclass. This is a kind of DAAT query parsing. Complex query like: #AND(a #OR(b c) d). First build object #AND, and eliminate "()". And get "a #OR(b c) d", if it's a term, build term object and append it to the current operator object. If it's a operator, call recursive.  

## Evaluation Results 

- Compare the top retrieved documents with top relative documents. So the precision and recall can be used. If ranking is not consideted. We call it P@n. The P and R will produce F measure. 

- If rankind is considered, NDCG@k is used. Gain divide discount(rank). RBP considers ranking than NDCG. 

- In web search, to compare two methods, we can use interleaving method combining ranking results from two methods. Like d11, d22, d16, d25, …. Use random bit to produce documents. The clicked results can be used for verification of better method. Like IoU.

## Information Need 
- Informational, transactional, navigation, commercial, local. 
- Web query: 1 to 3 words. 
- Unstructured query transform to structured queries with query language. 
- Use query will be formed into structured query by query processing and query reformulation. Query processing includes case conversion, stopword removal and stemming, abbreviations, phrases, spelling correction. Some search engine will consider the sequential dependency models. 

## Document Representation and Structure

- Description: convert documents tokens into index term. 
- Steps: format parsing(parse different page format into canonical format), then using NLP lexical analyzer transforms into index terms. Finally, the index terms will be indexed structure data. 
- Know controlled vocabulary. On the contrary, there is free text indexing. Using an uncontrolled vocabulary, and may miss sth. In Google, the full text indexing is used. The methods like tokens, stopwords, morphological processing is used in full text indexing. There are also urls, title, body and inlinks.  

## Best Match Method 

## 

