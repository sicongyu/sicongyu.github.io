---
layout: post
title:  "emplace() Uses Direct Initialization Instead of Uniform (List) Intialization"
date:   2021-12-23 24:41:11 +0800
categories: jekyll update
---
{% highlight cpp %}
#include <queue>
 
struct Aggregate_Type final
{
    int x;
    int y;
}
 
auto main() -> void
{
    std::queue<Aggregate_Type> queue_of_at;
    queue_of_at.emplace(3, 4); // You CANNOT do this.
}
{% endhighlight %}

Another example:    
{% highlight cpp %}
std::vector<std::vector<int>> v;
v.emplace_back(3, 4); // v[0] == {4, 4, 4}, not {3, 4} as in list-initialization
{% endhighlight %}