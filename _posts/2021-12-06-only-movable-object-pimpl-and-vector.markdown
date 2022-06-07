---
layout: post
title:  "Only-Movable Object, Pimpl and Vector"
date:   2021-12-06 01:27:11 +0800
categories: jekyll update
---
some-class.hpp:  
{% highlight cpp %}
#pragma once
 
#include "constraint.hpp"
 
#include <memory>
 
struct Some_Class : util::Only_Movable
{
    Some_Class();
    ~Some_Class(); // have to manually declare to use incomplete unique_ptr
 
    // Some_Class(const Some_Class&) = delete; // optional
    Some_Class(Some_Class&&); // hidden thanks to destructor, so manually claim one
 
    private:
    struct Impl;
    std::unique_ptr<Impl> impl;
};
{% endhighlight %}

main.cpp:  
{% highlight cpp %}
#include "some-class.hpp"
 
#include <vector>
 
auto main() -> int
{
    std::vector<Some_Class> test;
    for (size_t i = 0; i < 10 ; i++) { // to engage potential vector extension
        test.push_back(Some_Class{});
    }
}
{% endhighlight %}

Things to notice:

1. In order to use a incomplete unique_ptr you have to manually declare a destructor, but it's ok to implemented as = default in definition.
2. However, declaring a destructor will also hide implicit copy and move constructors, so you have to declare a move constructor manually, again, to be able to put the object into vector.
3. You'd better declare move constructor as noexcept to collaborate with std::move_if_noexcept of vector. But without a copy constructor it cannot fall back so it's ok to discard noexcept in this case.