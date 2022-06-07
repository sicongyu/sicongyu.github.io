---
layout: post
title:  "Note about Template Function/Class"
date:   2021-12-06 01:27:11 +0800
categories: jekyll update
---
1. Templates definition should be visible to users ahead of link stage i.e. should be put into header file or a .inl quoted by header file.  
2. Explicit specialization can hide their definitions in .cpp, but declarations have to happen before the first place the specialization could be instantiated.