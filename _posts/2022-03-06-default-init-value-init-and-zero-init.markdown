---
layout: post
title:  "Default Initialization, Value Initialization and Zero Initialization"
date:   2022-03-06 23:12:11 +0800
categories: jekyll update
---
[Default Initialization](https://en.cppreference.com/w/cpp/language/default_initialization "cppreference")  
[Value Initialization](https://en.cppreference.com/w/cpp/language/value_initialization "cppreference")  
[Zero Initialization](https://en.cppreference.com/w/cpp/language/zero_initialization "cppreference")  
[Default Member Initializer](https://en.cppreference.com/w/cpp/language/data_members "cppreference")  
[Trivial Default Constructor](https://en.cppreference.com/w/cpp/language/default_constructor "cppreference")  
{% highlight cpp %}

{
    struct T final
    {
        T() = delete;
        int x;
    };
 
    // T default_t; // Default init, illegal
    T value_t_1{}; // Value init -> default init, legal but value of x undetermined
    T value_t_2 = T{}; // The same as above
    // T value_t_3 = T(); // Illegal
}
 
{
    struct T final
    {
        T()
        {
            // do not init x intentionally
        }
        int x;
    };
 
    T default_t; // Default Init, default constructor called, value of x undetermined
    T value_t{}; // Value Init -> default init, value of x undetermined
}
 
{
    struct T final
    {
        T();
        int x;
    };
 
    T::T() = default; // Treated as user-provided constructor
 
    T value_t{}; // Value Init -> default init, zero init not performed, value of x undertermined
}
 
{
    struct T final
    {
        T() = default; // Compiler generated compiler and it does *nothing*, meaning it won't zero init x
        int x;
    };
 
    T default_t; // Default init, the default ctor is called, while value of x is undertermined
    T zero_t{}; // Value Init -> zero init, default constructor is not called, while value of x is zero
}
 
{
    struct T final
    {
        T() = default; // Non trivial default constructor, which makes value init not zeroed init
        int x{1};
    };
 
    T zero_t{}; // Value Init -> default init, default constructor is called, and value of x is 1 during default constructor
}
{% endhighlight %}