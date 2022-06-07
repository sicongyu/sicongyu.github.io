---
layout: post
title:  "Conversion Ambiguity"
date:   2021-12-06 01:27:11 +0800
categories: jekyll update
---
{% highlight cpp %}
#include <iostream>
 
template<class Derived>
struct Base
{
    operator const int&() const {
        std::cout << "conversion operator return const lvalue reference" << std::endl;
        return static_cast<const Derived*>(this)->data;
    }
 
    operator int&() {
        std::cout << "conversion operator return lvalue reference" << std::endl;
        return static_cast<Derived*>(this)->data;
    }
 
    operator int() {
        std::cout << "conversion operator return value" << std::endl;
        return static_cast<const Derived*>(this)->data;
    }
};
 
struct Derived_Test : Base<Derived_Test>
{
    Derived_Test(int input) : data(input) {}
    int data;
};
 
auto main() -> int
{
    Derived_Test test{0};
    const Derived_Test const_test{1};
    int& x = test; // evoke lvalue reference (conversion operator)
    const int& y = const_test; // evoke const lvalue reference
    // int z = test; // Compile failure: Ambiguous!
    int z = const_test; // evoke const lvalue reference
}
{% endhighlight %}