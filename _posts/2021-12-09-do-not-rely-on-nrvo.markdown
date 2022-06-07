---
layout: post
title:  "Do Not Rely on NRVO"
date:   2021-12-09 16:24:11 +0800
categories: jekyll update
---
{% highlight cpp %}
#include <iostream>
 
struct Foo
{
    Foo(int x);
    // Foo(const Foo&) noexcept;
    // Foo(Foo&&) noexcept;
    int x;
};
 
Foo::Foo(int x)
    : x(x)
{
 
}
 
// Foo::Foo(const Foo& other) noexcept
//     : x(other.x)
// {
//     std::cout << "copy constructor" << std::endl;
// }
 
// Foo::Foo(Foo&& other) noexcept
//     : x(other.x)
// {
//     std::cout << "move constructor" << std::endl;
// }
 
auto make_foo() -> Foo
{
    Foo result{1};
    std::cout << "the address within factory: " << &result << std::endl;
    return result;
}
 
auto main() -> int
{
    auto foo = make_foo();
    std::cout << "the address outside factory: " << &foo << std::endl;
}
{% endhighlight %}

Test output on arch, compiled with gcc,  

with user-defined ctors implementation: (same address, NRVO enabled)  
{% highlight bash %}
the address within factory: 0x7ffecd00e554
the address outside factory: 0x7ffecd00e554
{% endhighlight %}
with default ctors implementation: (different address, NRVO disabled)
{% highlight bash %}
the address within factory: 0x7ffe62d5d924
the address outside factory: 0x7ffe62d5d944
{% endhighlight %}