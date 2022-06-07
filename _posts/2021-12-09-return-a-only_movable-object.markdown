---
layout: post
title:  "Return a Only_Movable Object"
date:   2021-12-09 24:41:11 +0800
categories: jekyll update
---
{% highlight cpp %}
struct Foo fianl : util::Only_Movable
{
    // ... some members
}
 
auto return_foo() -> Foo
{
    Foo result;
 
    // ... do something
 
    return result;
}
{% endhighlight %}

The return may not trigger NRVO as NRVO is not mandatory, which means a "returned value" will be constructed via result, either through copy or move. However, this choice will be made after two resolutions, while during the first resolution result will be treated as if an rvalue, and move constructor is thus chosen.  

{% highlight cpp %}
auto main() -> int
{
    Foo foo_in_caller{return_foo()};
    // ... do something
}
{% endhighlight %}
However, copy elision when constructed from a prvalue is guaranteed, and a function call is a prvalue. So above expression only evokes one move constructor of Foo (if NRVO is not performed by compiler), which happens during the construction from result to the "returned value", while the construction from the "returned value" to foo_in_caller is guaranteed to be elided.