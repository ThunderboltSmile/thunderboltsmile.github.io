---
title: python.cookbook读书笔记二
date: 2017-10-03
categories: 
  - python
tags: 
  - python
  - 读书笔记
---

第一章主要包含的内容是以数据结构为主，在前五节中主要了解了解构赋值和解构赋值中占位符`_`和`*`的使用方法，以及在保留有限个数的元素时`dequeue`的用法，最后是学习了`heapq`的使用情境及方法

<!--more-->

#### 1.6 将dictionary中的key对应到多个数值
通常情况下，字典中的键值对是一一对应的关系，当我们想要在字典中使用一个键来保存多个值时，就需要将这多个值使用其他的容器包装起来，比如list和set，到底是使用list还是set要根据具体情况而定，当使用者想要按照一定顺序排列这些值时，就是用list，当想要避免值重复时，就是用set。

原则上讲，创建一个一键多值的字典并不困难，但是初始化这个字典的却会是一件繁琐的事，因此我们应该使用defaultdict来创建这样的数据结构
使用defaultdict创建的字典会在使用者访问起不存在的键名时自动创建和初始化这个键名对应的值
```python
from collections import defaultdict
d=defaultdict(list)
f=defaultdict(set)

d['a'].append('a')
d['a'].append('b')
d['a'].append('b')
d['a'].append('c')

f['a'].add('a')
f['a'].add('b')
f['a'].add('b')
f['a'].add['c']

d['a'] #['a','b','b','c']
f['a'] #{'b','c','a'}
```
#### 1.7 让字典内容保持一定的顺序
##### solution
当我们想要控制字典中内容的顺序时，可以使用`collections`模块儿中的`OrderedDict`，其在遍历时会按照数据插入的顺序进行。
##### discussion
`OrderedDict`在内部会同时保存一个记录元素插入顺序的list，当一条数据被新插入字典中时，会被记录在list的末尾，重新对该键进行赋值时，list的顺序不会发生变化。因此，`OrderedDict`的大小会比普通字典的两倍还要大，这在使用时应该加以权衡。

#### 1.8 对字典内容进行计算
当我们想要对字典内容进行计算时，可能免不了要同时获得字典内容的键名和键值，这其实是一件挺麻烦的事，尤其是对于诸如找出字典中最大值或者最小值这种操作来讲。这种时候，我们可能就需要一种发放直接将字典转换成list来进行比较。
在`collections`模块儿中有一种方法叫做`zip`，这种方法可以讲字典内容转换成list，如下所示
```python
prices={
	'ACME':45.23,
	'AAPL':612.78,
	'IBM':205.55
}
prices_sorted = sorted(zip(prices.values(),prices.keys()))
#prices_sorted is [(45.23,'ACME'),(205.55,'IBM'),(612.78,'AAPL')]
min_price = min(zip(prices.values(),prices.keys()))
#min_price is (45.23,'ACME')
```
##### discussion
zip会将字典键值对转化为以给定内容和形式为单位的序列，并可以对其进行遍历。
应当注意的是，zip每次会产生一个只能使用一次的iterator，当本次循环结束后，再次使用这个iterator会出错。

#### 1.9寻找两个字典中的公有量
在python中，可以将其键和值提取出来作为集合进行运算，通常使用的方法有`dict.keys()`、`dict.items()`。
如下所示：
```python
a={
	'x':1,
	'y':2,
	'z':3,
}
b={
	'w':10,
	'x':11,
	'y':2
}
# use keys() or items() to filter dict
# find keys in common
a.keys() & b.keys() #{'x','y'}
# find keys in a that are not in b
a.keys() - b.keys() #{'z'}
# find (key,value) pairs in common
a.items() & b.items() #{('y',2)} 

# make a dict with certain keys removed
c={key:a[key] for key in a.keys() - {'z','w'}}
# c is {'x':1,'y':2}
```
由于字典的`value`不能保证唯一性，因此不能对其进行集合运算。

#### 1.10 保持序列顺序的同时删除重复的元素
如果仅仅是想要删除序列中重复的部分，那么使用`set`即可很方便地解决，但是set并不能保证结果的有序性，因此我们采用generator函数的方式来得到去重后的结果，如下所示：
```python
def dedupe(items, key=None):
    seen = set()
    for item in items:
        val=item if key is None else key(item)
        if val not in seen:
            yield item
            seen.add(val)

a=[{'x':1,'y':2,'z':3},{'x':2,'y':3,'z':5},{'x':1,'y':2,'z':3}]
print(list(dedupe(a,key=lambda d: (d['x'],d['y']))))
#[{'x': 1, 'y': 2, 'z': 3}, {'x': 2, 'y': 3, 'z': 5}]
```
解释：我们利用set的特性进行去重，但是以generator的形式来从序列中依次取得未重复的元素，当序列元素为unhashable时，可以传入参数函数key对其进行处理。

