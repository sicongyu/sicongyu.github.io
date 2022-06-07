---
layout: post
title:  "util::recyclable_ptr"
date:   2022-05-15 23:50:11 +0800
categories: jekyll update
---
An object pool design in the form of std::xxx_ptr.

## Type: 
{% highlight cpp %}
template <class Resource_Type>
struct recyclable_ptr final : util::Only_Movable
{% endhighlight %}

## Constructors:    
{% highlight cpp %}
template <class T>
recyclable_ptr<T>() noexcept; (1)
 
template <class T>
recyclable_ptr<T>(nullptr_t) noexcept; (1)
 
template <class T>
recyclable_ptr<T>(recyclable_ptr<T>&& other) noexcept; (2)
 
template <class T>
recyclable_ptr<T>(std::unique_ptr<T>&& ptr, Pool* pool) noexcept; (3)
{% endhighlight %}
(1) Constructs an empty recyclable_ptr of type T.

(2) Move constructs a recyclable_ptr of type T from other of the same type. other will be set empty after construction.

(3) Constructs a recyclable_ptr of type T from a unique_ptr of the same type. This constructor is private and could be only evoked by interface functions below.

## Interface:
{% highlight cpp %}
template<class Resource_Type, class Fn>
auto make_recyclable(Fn fn) -> recyclable_ptr<Resource_Type>; (1)
 
template<
    class Resource_Type, class Fn, class Arg,
    class Hash = std::hash<std::remove_reference_t<Arg>>,
    class KeyEqual = std::equal_to<std::remove_reference_t<Arg>>
>
auto make_recyclable(Fn fn, Arg&& arg) -> recyclable_ptr<Resource_Type>; (2)
{% endhighlight %}

Both interface functions accept their first argument as a factory function provided by user.

(1) is chosen when the factory function has no parameters, in which case a unique pool will be created distinguished by the type of Fn. Lambda is recommended for creating such factory functions, for their anonymous nature.

(2) is chosen when the factory function has one and only one parameter. Under such circumstance the value of arg needs distinguishing as well on top of the type of Fn. A hash function and a comparison function thus is needed to map the value of arg to a specific pool. std::hash and std::equal_to will be used if user fails to provide them. If the user-provided or defaulted hash/comparison function is disabled/ill-formed, a fallback hash/comparison function is used, which is basing on the address of arg. Thus it is required that the life cycles of args provided for different pools are overlapped.

*Interface for factory functions with more than one parameters is hard to implement due to the difficulty to implement effective universal hash functions for multiple inputs. Capturing extra arguments into the factory function object is a recommended workaround.