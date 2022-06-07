---
layout: post
title:  "The 'void_t' Pattern for Type Traits Implementation"
date:   2022-05-31 20:55:11 +0800
categories: jekyll update
---
Example Implementation:  
{% highlight cpp %}
template< class ... > using void_t = void;
 
// primary class template
template<class , class = void>
struct has_member : std::false_type
{ };
 
// specialized as has_member< T , void > or discarded (sfinae)
template<class T>
struct has_member< T , void_t< decltype( T::member ) > > : std::true_type
{ };
 
// helper type
template<class T>
constexpr bool has_member_v = has_member<T>::value;
{% endhighlight %}

Example Usage:    
{% highlight cpp %}
class A {
public:
    int member;
};
 
class B {
};
 
static_assert( has_member< A >::value , "A" );
static_assert( has_member< B >::value , "B" );
{% endhighlight %}  
Simplified Explanation:  

* SFINAE also happens during partial specialization resolution.
* void_t is only evaluated when all template arguments are well-formed.
* The template argument in an expression will be expanded (if possible) by default argument provided in the primary template before any overload/specialization resolution, i.e. "has_member<A>" will be expanded as "has_member<A, void>" first.
* During the template parameter/argument matching check after template parameter deduction for all (primary and specialization) candidates, substituted parameter is checked against the expanded arguments. As "has_member<A>::value" has been expanded as "has_member<A, void>::value", it matches "has_member<A, void_t<decltype(A::member)>>", and thus the specialization is chosen.  
  
[Fully Explanation](https://stackoverflow.com/questions/27687389/how-does-void-t-work "Stackoverflow")