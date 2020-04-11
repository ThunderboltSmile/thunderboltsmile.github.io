---
title: python.cookbook读书笔记五
date: 2017-10-23
categories: 
  - python
tags: 
  - python
  - 读书笔记
---
### chapter4 iterators and generators
Iteration是python中最强大的特性之一，很多时候，你有可能只是看到使用iterator来产生特定的序列。然而，iterator还有更多的可能，比如一些非常好用的工具函数，创建自己的generator函数等等，这一章将主要介绍使用iteration时会遇到的常见问题。
<!-- more -->

#### 4.1 manually consuming a iterator

##### problem
你想处理iterator中的数据，但由于某些原因不想使用for循环
##### solution
使用next方法可以依次得到iterator的各个元素
```python
with open('/etc/password') as f:
    try:
        while True :
            line=next(f)
            print(line,end='')
        except StopIteration:
            pass
```
StopException用来标记迭代结束的情况，同样可以使用在line为None时break的方法结束循环。

##### discussion
在使用next方法时，其实是调用了iterator的`__next__`方法，当我们使用iter方法初始化某个iterable，我们实际调用的是`__iter__`方法。

#### 4.2 delegeting iteration

##### problem
在你自定义的容器中，保存了一个iterable的数据结构，你想要使得这个容器拥有iterable的性质，即可以通过对容器的迭代来获取内部这个iterable数据块的内容。
##### solution
结合上一节，在进行迭代操作时，实际调用了`__iter__`方法，因此你只需要在容器中自定义`__iter__`方法即可：
```python
class Node:
    def __init__(self,value):
        self._value=value
        self._children=[]
    def __repr__(self):
        return 'Node({!r})'.format(self._value)
    def add_child(self,node):
        self.children.append(node)
    def __iter__(self):
        return iter(self._children)
if __name__ == '__main__':
    root=Node(0)
    child1=Node(1)
    child2=Node(2)
    root.add_child(child1)
    root.add_child(child2)
    for ch in root:
        print(ch)
    #outputs Node(1),Node(2)
```
##### discussion
python的迭代器协议要求被迭代对象必须要有`__iter__`方法，并且该方法必须返回一个拥有next方法以定义iteration具体行为的结构。
就如len(s)实际调用了`s.__len__`，iter(s)实际调用的是`s.__iter__()`。

#### 4.3 Creating New Iteration Patterns With Generators
##### problem
你想要实现一种与内置方式不同的迭代模式。
##### solution
Generator提供了完美的解决方案！
```python
def frange(start,stop,increment):
    x=start
    while(x<stop):
        yield x
        x+=increment
for x in frange(1,6,2):
    print(x)
#1,3,5
list(frange(1,6,2))
#[1,3,5]
```
##### discussion
我们定义一个generator函数，关键字为yield，只要出现了这样的关键字，那么表示，这个函数的返回值会拥有next方法，即可移植性迭代操作。

#### 4.4 Implementing the Iterator Protocal
##### problem
在自定义容器中，想要对深层次的可迭代对象进行迭代操作，比如对外层对象进行迭代的结果仍然是iterable的，就再次进行迭代。
##### solution
yield from 方法可以实现这类要求
```python
#在4.2中，内置的__iter__返回了序列的外层迭代接口，但我们想要一个接口可已进行深层的迭代
class Node:
    def __init__(self,value):
        self._value=value
        self._children=[]
    def __repr__(self):
        return 'Node({})'.format(self._value)
    def add_child(self,node):
        self._children.append(node)
    def __iter__(self):
        return iter(self._children)
    def depth_iter(self):
        yield self
        for s in self:
            yield from s.depth_iter()
if __name__=='__main__':
    root=Node(0)
    child1=Node(1)
    child2=Node(2)
    root.add_child(Node1)
    root.add_child(Node2)
    child3=Node(3)
    child4=Node(4)
    child1.add_child(child3)
    child2.add_child(child4)
    for ch in root.depth_iter():
        print(ch)
#Node(1),Node(3),Node(2),Node(4)
```
##### discussion
yield from 是python3的语法，`yield from gen()`，其中gen()必须返回一个generator对象，此时会执行返回对象的next方法并将结果传递给外层的yield。

#### 4.5 Iterating In Reverse
##### problem
你想要对某个序列进行反向迭代
##### solution
使用内置的`reversed`方法可以解决此类问题
```python
a=[1,2,3,4,5]
for x in reversed(a):
    print(x)
#54321
```
##### discussion
使用reversed的方法必须要满足两个条件中的一个，其一是传入的参数对象必须要有明确的大小，也就是能够确定iterate的次
数，然后就是对象具有`__reversed__`方法，如果这两个条件都不满足，那么你需要先将该对象转化为list在进行reversed。
但是，将iterator对象转化为reversed会消耗额外的内存，因此不提倡这样做，可以在对象中定义`__reversed__`方法来自定义反向迭代的逻辑。



