---
layout: post
title:  "Friend Declarations shall not Declare Partial Specializations"
date:   2022-04-03 22:32:11 +0800
categories: jekyll update
---
In other words, code below is forbidden (this is the case for both friend template classes and ~~functions~~ (function template partial specialization is not allowed in any cases!):

{% highlight cpp %}
template<typename T, typename U>
class Y {
};
 
class X {
    public:
        X(int value) : i(value) {}
        const int& getI()    const { return i; }
    private:
        int    i;
        template<class U>
        friend class Y<X,U>; // Not Allowed!
};
{% endhighlight %}

BTW, you will need both forward declarations if the friend template function/class uses the friended type:

{% highlight cpp %}
template <typename T>
class Obj;
 
template <typename T>
Obj<T> make_obj(T t);
 
template <typename T>
class Obj {
private:
    T t;
    Obj (T & t) : t(t) { }
    Obj() = delete;
 
    friend Obj make_obj<T>(T t); // or Obj<T> as return value, where T can be omitted to ease some pain. However, the T in make_obj<T> shall not be omitted.
};
 
template <typename T>
Obj<T> make_obj(T t) {
    return Obj<T>(t);
}
{% endhighlight %}

Alternatively, you can omit forward declarations by using return type deduction for the friend function, i.e. auto:

{% highlight cpp %}
template <typename T>
class Obj {
private:
    T t;
    Obj (T & t) : t(t) { }
    Obj() = delete;
 
    friend auto make_obj<T>(T t);
};
 
template <typename T>
auto make_obj(T t) {
    return Obj<T>(t);
}
{% endhighlight %}