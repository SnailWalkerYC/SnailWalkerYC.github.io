---
layout: post
title: Natural Language Processing Cheat Sheet
date: 2019-07-30
Author: 来自中世界
categories: [technology]
tags: [nlp]
comments: true
---

This course is taught [Alan Black](http://www.cs.cmu.edu/~awb/) by from [here](http://demo.clab.cs.cmu.edu/NLP/). He is an excellent professor.  



### Language Model

- In natural language, there are multiple layers, including morphology (analysis of words into meaningful components), lexemes(part of speech tagging, words with multiple meanings), syntax, semantics(deal with scope ambiguity), pragmatics(non-local meaning), discourse(context sequence). 

- NLP application: information extraction, NER, reference resolution, relation detection, information retrieval, question-answer system.

- NL morphology: use finite state automaton to represent the word regular form, plural, or irreg-sg-noun. Besides, finite state transducers can also represented for morphological parsing. (Table, trie, fst) -> stemming, tokenization. So morphology can be used for word parsing in sentences. 

- Language model: see one sentence probability. So even the words in two sentences may be the same, but the real sentence should be higher probability. Be careful about the stoped word. With language model, we can get the generated sentence. Unigram, bigram, trigram model. 

  ![gram1](../images/gram1.png)

  ![gram2](../images/gram2.png)

   To know the perplexity:

  ![gram3](../images/gram3.png)

  - LM smoothing: add one to all with known vocabulary. But for OOV, it can not be represented. Can set one word, UNKNOWN. 

  - Good-Turing Smoothing: update count number. $c = (c+1) \frac {(N_{c+1)}} {N_c}$

  - Backoff: no trigram, using bigram, no bigram, use unigram, no unigram, smooth.

### Classification

- Noise channel:


​    