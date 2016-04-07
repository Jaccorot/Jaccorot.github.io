---
layout: post
title:  "Python排序算法归纳"
date:   2016-04-06 18:00:00 +0800
categories: python sorting
---

最简单的排序有三种：插入排序，选择排序和冒泡排序。这三种排序比较简单，它们的平均时间复杂度均为O(n^2)，在这里对原理就不加赘述了。贴出来源代码。

* 插入排序：
{% highlight python linenos %}
    def insertion_sort(sort_list):
        iter_len = len(sort_list)
        if iter_len < 2:
            return sort_list
        for i in range(1, iter_len):
            key = sort_list[i]
            j = i - 1
            while j>=0 and sort_list[j]>key:
                sort_list[j+1] = sort_list[j]
                j -= 1
            sort_list[j+1] = key
        return sort_list
{% endhighlight %} 

* 冒泡排序：
{% highlight python linenos %}
    def bubble_sort(sort_list):
        iter_len = len(sort_list)
        if iter_len < 2:
            return sort_list
        for i in range(iter_len-1):
            for j in range(iter_len-i-1):
                if sort_list[j] > sort_list[j+1]:
                    sort_list[j], sort_list[j+1] = sort_list[j+1], sort_list[j]
        return sort_list
{% endhighlight %} 

* 选择排序：
{% highlight python linenos %}
    def selection_sort(sort_list):
        iter_len = len(sort_list)
        if iter_len < 2:
            return sort_list
        for i in range(iter_len-1):
            smallest = sort_list[i]
            location = i
            for j in range(i, iter_len):
                if sort_list[j] < smallest:
                    smallest = sort_list[j]
                    location = j
            if i != location:
                sort_list[i], sort_list[location] = sort_list[location], sort_list[i]
        return sort_list
{% endhighlight %} 