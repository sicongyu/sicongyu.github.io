---
layout: post
title:  "STL Containers Does Not Support Direct Initialization through std::initializer_list"
date:   2021-12-06 01:27:11 +0800
categories: jekyll update
---
{% highlight cpp %}
#include "util/constraint.hpp"
#include <list>
#include <iostream>
 
using namespace ss;
 
struct Foo final :
// util::Only_Movable // uncomment this line will cause compiling failure
{
    Foo(int);
};
 
Foo::Foo(int x)
{
    std::cout << "Foo is constructed with " << x << std::endl;
}
 
struct Bar final
{
    Bar();
    std::list<Foo> list;
};
 
Bar::Bar()
    : list{1, 2, 3} // {1, 2, 3} evokes std::list(std::initializer_list<T>, ...) which initialize the list through copy as opposite of direct initialization (i.e. "emplace"), and thus requires the copy constructor of T.
{
    // list.emplace(1);
    // list.emplace(2);
    // list.emplace(3);
    // uncomment these lines if you make Foo only_movable
}
 
auto main() -> int
{
    Bar bar{};
}
{% endhighlight %}