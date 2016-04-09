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


平均时间复杂度为O(nlogn)的算法有：归并排序，堆排序和快速排序。

* 归并排序
对于一个子序列，分成两份，比较两份的第一个元素，小者弹出，然后重复这个过程。对于待排序列，以中间值分成左右两个序列，然后对于各子序列再递归调用。源代码如下，由于有工具函数，所以写成了callable的类：

{% highlight python linenos %}
class merge_sort(object):
    def _merge(self, alist, p, q, r):
        left = alist[p:q+1]
        right = alist[q+1:r+1]
        for i in range(p, r+1):
            if len(left)>0 and len(right)>0:
                if left[0]<=right[0]:
                    alist[i] = left.pop(0)
                else:
                    alist[i] = right.pop(0)
            elif len(right)==0:
                alist[i] = left.pop(0)
            elif len(left)==0:
                alist[i] = right.pop(0)
 
    def _merge_sort(self, alist, p, r):
        if p<r:
            q = int((p+r)/2)
            self._merge_sort(alist, p, q)
            self._merge_sort(alist, q+1, r)
            self._merge(alist, p, q, r)
 
    def __call__(self, sort_list):
        self._merge_sort(sort_list, 0, len(sort_list)-1)
        return sort_list
{% endhighlight %} 


* 堆排序

是建立在数据结构——堆上的。关于堆的基本概念、以及堆的存储方式这里不作介绍。这里用一个列表来存储堆（和用数组存储类似），对于处在i位置的元素，2*i+1位置上的是其左孩子，2*i+2是其右孩子，类似得可以得出该元素的父元素。

首先我们写一个函数，对于某个子树，从根节点开始，如果其值小于子节点的值，就交换其值。用此方法来递归其子树。接着，我们对于堆的所有非叶节点，自下而上调用先前所述的函数，得到一个树，对于每个节点（非叶节点），它都大于其子节点。（其实这是建立最大堆的过程）在完成之后，将列表的头元素和尾元素调换顺序，这样列表的最后一位就是最大的数，接着在对列表的0到n-1部分再调用以上建立最大堆的过程。最后得到堆排序完成的列表。以下是源代码：

{% highlight python linenos %}
class heap_sort(object):
    def _left(self, i):
        return 2*i+1
    def _right(self, i):
        return 2*i+2
    def _parent(self, i):
        if i%2==1:
            return int(i/2)
        else:
            return i/2-1
     
    def _max_heapify(self, alist, i, heap_size=None):
        length = len(alist)
         
        if heap_size is None:
            heap_size = length
 
        l = self._left(i)
        r = self._right(i)
 
        if lalist[i]:
            largest = l
        else:
            largest = i
        if ralist[largest]:
            largest = r
 
        if largest!=i:
            alist[i], alist[largest] = alist[largest], alist[i]
            self._max_heapify(alist, largest, heap_size)
 
    def _build_max_heap(self, alist):
        roop_end = int(len(alist)/2)
        for i in range(0, roop_end)[::-1]:
            self._max_heapify(alist, i)
 
    def __call__(self, sort_list):
        self._build_max_heap(sort_list)
        heap_size = len(sort_list)
        for i in range(1, len(sort_list))[::-1]:
            sort_list[0], sort_list[i] = sort_list[i], sort_list[0]
            heap_size -= 1
            self._max_heapify(sort_list, 0, heap_size)
 
        return sort_list
{% endhighlight %} 