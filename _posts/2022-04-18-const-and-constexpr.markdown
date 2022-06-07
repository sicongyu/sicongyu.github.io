---
layout: post
title:  "const and constexpr"
date:   2022-04-18 17:53:11 +0800
categories: jekyll update
---
* constexpr is not the only way to create a constant expression. In the following statement "const int some_constant = 5;", some_constant is a constant expression, A constant expression may also be created without specifying constexpr, as long as:
    * some_constant has integral type
    * some_constant is specified as const
    * some_constant is initialized with another constant expression (i.e. literal "5" in this case)
* both const and constexpr imply internal linkage
* for variables, constexpr implies const, but this is not the case for non-static member functions after C++14.  
  
[Reference](https://stackoverflow.com/questions/14116003/whats-the-difference-between-constexpr-and-const "Stackoverflow")