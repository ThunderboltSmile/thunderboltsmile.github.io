---
title: python.cookbook读书笔记一
date: 2017-09-27
categories: 
  - python
tags: 
  - python
  - 读书笔记
---

## chapter1 数据结构和算法
python内置了许多有用的数据结构，比如list,set,dictionarie，大部分时候我们只是直接的使用这些结构，但是，当面对类似搜索、排序、筛选、赋值等操作时，会遇到仅仅依靠数据结构无法解决的问题，本章的目的正是讨论这些公有数据结构以及相关的算法知识。

<!-- more -->

### 1.1解构赋值
#### 问题
当你想要将含有N个数值的序列同时复制给M个变量时，可以使用简单的赋值语句来完成，仅需要满足的条件是N==M
```python
p=(3,6,9,11)
x,y,z,a=p
//等价于x=3,y=6,z=9,a=11

data=['a','b','c',3]
a,b,c,d=data
//等价于a='a',b='b',c='c',d=3
```
如果N!=M，则会抛出错误
#### 讨论
解构赋值可以使用在任何iterable的数据类型，包括元组、序列、字符串、文件列、迭代器等如：
```python
s='hello'
a,b,c,d,e=s
//相当于a='h',b='e',c='l',d='l',e='o'
```
当你想要忽略iterable中的某些选项的时候，在赋值时在相应位置使用占位符`_`代替即可。
```python
data=['a','b','c','d']
_,a,_,b=data
//相当于a='b',b='d'
```

### 1.2任意长度对象的解构赋值
#### 问题
在1.1中，若`N>M`，会抛出`too many values to unpack`的错误
##### 解决方法
使用星号表达式可以解决这类问题  
```python
def drop_first_last(grades):
    first,*middle,last = grades
    return avg(middle)
#将返回grades除去了头和尾后剩下的部分

record = ['a','b','c','d','e','f']
a,*b,c,d,e = record
#相当于a='a',b=['b','c'],c='d',d='e',e='f'
```
使用星号表达式作为前缀的变量会获得将‘剩余的所有待分配值’，当然会按照相对位置优先分配给没有使用星号的变量。
#### 讨论
扩展后的解构赋值通常是未某些情况量身定制的，比如我们想要从某个数据集合中取出特定位置的部分，例如某个序列中除了某些特殊位置外全都是数字之类的情况。星号表达式特别适用于处理包含元组的序列，也适合于处理包含特定分隔符的字符串序列
```python
records=[('a',1,2,3),('b',2,3,4),('c',3,4,5)]
for tag,*args in records:
    print(tag,args)
#'a',1,2,3
#'b',2,3,4
#'c',3,4,5

line='abcd/efgh/ijkl/mnop/qrst'
a,b,*c,d=line.split('/')
#a='abcd'
#b='efgh'
#c='ijkl/mnop'
#d='qrst'
```
有时你想要在选择出有用的部分同时去除不需要的部分，同样可以使用`_`来代替相关的位置
```python
lis=['a','b',('c','d','e')]
a,*_,(*_,c)=lis
#a='a'
#c='e'
```
### 1.3保留最后几个值
#### problem
有时你想要在某些过程中保留数据中的最后几个值
#### solution
保留有限个数的历史记录非常适合使用`deeque`这种数据结构，例如以下就是一个简单的例子：
```python
from collections import deque
#引入deque
def search(lines,pattern,history=5):
    previous_lines=deque(maxlen=history)
        for line in lines:
            if pattern in line:
                yield line,previous_lines
            previous_lines.append(line)

if __name__ == '__main__':
    with open('./test.txt') as f:
        for line,prevlines in search(f,'asdf',1):
            for pline in prevlines:
                print(pline,end='')
                print(line,end='')
                print('-'*20)
```
#### 讨论
上述代码将文件内容进行分析，取出与搜索目标即pattern相关的行以及相关行的前几行（history），使用了yield关键字。这里控制输出行数的方法是定义一个限定长度的deque，在每一次想deque中添加数据时，会将超出限度的数据清除。
deque的常用方法有：

* append 向末尾追加数据
* appendleft 向左侧添加数据
* pop 清除末尾元素
* popleft 清除开头元素
* extend 向末尾添加集合元素（即向数组中添加整个数组的值）
* extendleft 顾名思义

### 1.4寻找多个最值
#### problem
请给我找出由某个数据集合中最大或者最小值组成的list
#### solution
这种情况下使用heapq最为合适，这种内置的数据结构天生具有类似的能力
```python
import heapq
nums=[1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]
print(heapq.nlargest(3,nums)) #prints [42,37,23]
print(heapq.nsmallest(3,nums)) #prints [-4,1,2]
#heapq也可以处理较为复杂的结构化数据
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
cheap = heapq.nsmallest(3, portfolio, key=lambda s: s['price'])
expensive = heapq.nlargest(3, portfolio, key=lambda s: s['price'])
```
#### discussion
heapq又称为堆，是一种python模块，这种结构的骨架为一颗二叉树，其中每个父节点的值都小于或者等于其任意一个子节点，因此整个堆的最小值一定是在顶点。从数据类型角度讲，heapq是一类数组，这类数组中一定存在着这种关系，即：`for k in list(heap.length):heap[k] <= heap[2*k+1]&&heap[k]<=heap[2*k+2]`
heapq的常用操作有：

* heapq.heappush(heap,item) 将item添加至heap中
* heapq.heappop(heap) 将堆顶的值弹出，即弹出并返回heap中的最小值
* heapq.heappushpop(heap,item) 将item添加至heap中，然后立刻将heap最小的值弹出并返回，效率比两个操作分开执行要高
* heapq.heapify(x) transform x into a heap,in-place,in linear time
* heapq.heapreplace(heap,item) 先返回现有heap的最小值，再将item添加进heap中
* heapq.merge(*iterables,key=None,reverse=False)将多个排好序的输入merge为一个排好序的输出，返回的是iterable数据结构，每次按顺序输出尚未输出过的数据中的最小值

* heap.nsmallest(n,iterable,key=None) 返回由iterable定义的数据中最小的n个数，若提供了key，则会以key作为取值索引进行比较
* heapq.nlargest(n,iterable,key=none) 顾名思义

使用heapq的场景多见于从不太大型的集合中取得多个特定数据，当只需要一个最值或者集合规模比较庞大时，更快的方法是采用min()或者max(),如果想要获取的最值个数与集合本身元素数量在一个数量级，更快的方式则是使用sort后再slice。如sorted(items)[:N]

### 1.5 完成有序优先级的序列
#### problem
你想要按自定义的优先级填充某个序列，并总是返回序列最优先的拿一个值
#### solution
同上，这类问题也可以使用heapq来解决，在填充的时候利用heapq的比对特点来进行优先级的设置。
```python
import heapq
class PriorityQueue:
    def __init__(self):
        self._queue=[]
        self._index=0
    def push(self,item,priority):
        heapq.heappush(self._queue,(-priority,self._index,item))
        self._index += 1
    def pop(self):
        heapq.heappop(self._queue)[-1]
```
注意，我们可以明确一点即在python中存在`if a>b : (a,any,any)>(b,any,any)`成立，因此我们可以将优先级作为item的第一个数值以便于heapq进行排序。
下面看看如何使用上面的代码来解决本节的问题：
```python
class Item:
    def __init__(self,name):
        self._name = name
    def __repr__(self):
        return 'Item({!r})'.format(self._name)

q=PriorityQueue()
q.push(Item('foo'),1)
q.push(Item('bar'),2)
q.push(Item('span'),3)
q.pop()#Item('span')
q.pop()#Item('bar')
```
#### discussion
heapq默认的pop方法会返回最小的元素，而我们将之以负数代替即可达到范围我们认为的最大的元素。在我们定义元素时，还用到了自增序列索引值·`index`，它的作用是在我们连续传入两个相同优先级的元素时按照索引来进行排序，否则会抛出`无法排序`错误。

