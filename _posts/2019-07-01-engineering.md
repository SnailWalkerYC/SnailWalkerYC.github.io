---
layout: post
title: Some thoughts about engineering
date: 2019-07-01
Author: Snail Walker
categories: [technology]
tags: [engineering]
comments: true
---


- Installing dependency: before download a new dependency, see if current version has conflict with installing version. Not change anything about current environment.  
- The newest version maybe not be the best version, the suitable version is the best, which is decouple with current version.
- The modification: less is better, do not try to modify too much at one time.
- make clean: most of projects will cache the result like so,o, when you change too much, remember make clean, Qt Creator is so evil. For instance, when there is error about no shared library, but you delete it. Using make clean.
- If you forget the above steps. try every step on web to make the code working again. 
- Most lib uses Protocobuf 2.0, so do not change it to 3.0. 
- Rewrite the whole code when you see the code is not beautiful as you like.
ldconfig: sudo ldconfig will make the 
- Love & Peace~
