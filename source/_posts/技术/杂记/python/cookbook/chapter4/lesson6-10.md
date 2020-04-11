---
title: python.cookbook读书笔记六
date: 2017-10-26
categories: 
  - python
tags: 
  - python
  - 读书笔记
---
前面五小节讲述的是iterator的基本特性以及一些常用技巧：
    
    4.1 manually consuming a iterator--next方法
    4.2 delegeting iteration--定义__iter__方法
    4.3 Creating New Iteration Patterns With Generators--yield
    4.4 Implementing the Iterator Protocal--yield from
    4.5 Iterating In Reverse--定义__reversed__方法

<!-- more -->
### 4.6. Defining Generator Functions with Extra State
#### problem
你想要将从外部获取的信息以iterator的形式展示
#### solution
定义class并搭配__iter__方法是解决这类问题的首选
```python
from collections import deque
class linehistory:
    def __init__(self,lines,len=3):
        self.history=deque(len)
        self.lines=lines
    def __iter__(self):
        for lineno,line in self.lines:
            self.history.append((lineno,line))
            yield line
    def clear(self):
        self.history.clear()
````
iter方法定义了对该class的示例进行迭代操作时的数据获取逻辑
```python
with open('test.txt','rt') as f:
    lines=linehistory(f)
    for line in lines:
        if 'python' in line:
            for lineno,hline in lines.history:
                print('{}:{}'.format(lineno,hline),end='')
```
以上代码将test.txt读入并初始化一个linehistory对象，查找该文件中是否包含python字符串，若包含则输出该字符串以及其所在的行号。
#### discussion
在需要对迭代对象进行复杂的处理逻辑或是与其他对象进行交互时，上述方法应该是首选的iteration方案。但有个潜在缺点是不能对实例化的对象直接进行next操作，而需要先使用iter()函数进行处理再调用next方法
```python
next(lines)#error
it=iter(lines)
next(it)#right
```

### 4.7. Taking a Slice of an Iterator
#### problem
你想要给某个iterator进行像list那样的切片操作，但是iterator对象原生是不支持slice操作的。
#### solution
itertools中有个islice方法正式为满足此类需求而产生的。
```python
def Count(n):
    while True:
        yield n
        n+=1
c=Count(0)
c[1:3]#报错
cs=itertools.islice(c,1,3)#正确方式
```
#### discussion
itertools中的islice可以像切片操作一样返回特定迭代次数之间的数据，但应该强调的是，这个操作的机制是使用iterator并将切片起点次数之前的值舍弃，一直迭代获取到切片结束为止。这会改变被操作的iterator对象。因此，如果想要保留iterator中的值，那还是应该先将其转化为list在进行切片操作。

### 4.8. Skipping the First Part of an Iterable
#### problem
你想要在某个循环的过程中跳过某几行,比如文件开头几行的注释说明。
#### solution
当然啦，则个问题用上面的itertools.islice也是可以解决的。但是我们想要更优雅的方式，怎么办呢，itertools提供了dropwhile函数，可以跳过某些元素。
```python
from itertools import dropwhile
with open('test.txt') as f:
    for line in dropwhile(lambda:line:line.startswith("#"),f):
        print(line,end='')
```
如果不使用dropwhile的话，可能会出现这样的代码：
```python
with open('test.txt') as f:
    while True:
        line=next(f)
        if line.startswith("#"):
            break
    while line:
        print(line,end='')
        line=next(f,None)
```
如果使用表达式进行筛选，那可能会把所有注释都删掉，显然这不是我们想要的：
```python
with open('test.txt') as f:
    lines=(line for line in f if not line.startswith("#"))
    for line in lines:
        print(line,end='')
````
而我们只想删掉开头的几句注释而已。
#### discussion
本小节的方法适用于所有iterator对象，包括那些无法确定大小的，以及所有拥有迭代器的对象。

### 4.9. Iterating Over All Possible Combinations or Permutations
#### problem
你想要获得iterator中所有item的所有的排列和组合的结果
#### solution
itertools提供了用来排序的permutations函数和用来组合的combination函数。
```python
from itertools import permutations
items=['a','b','c']
for item in permutaions(items):
    print(item)
#('a','b','c')
#('a','c','b')
#('b','a','c')
#....其他排列结果

for item in permutaions(items,2):
    print(item)
#('a','b')
#('a','c')
#('b','c')
#其他排列结果
```
permutation还可以接受一个额外的参数用来控制每种排列中元素的个数。
```python
from itertools import combinations
for item in combinations(items):
    print(item)
#('a','b','c')
for item in combinations(items,2):
    print(item)
#('a','b')
#('a','c')
#其他组合结果
```
如果想要获得元素可重复的组合结果，可以使用combinations_with_replacement
```python
from itertools import combinations_with_replacement
for item in combinations_with_replacement(items,2):
    print(item)
#('a','a')
#('a','b')
#('b','b')
#('b','c')
#其他组合结果
```
#### solution
这一小节只是展示了itertools中的一部分功能，当你遇到需要对多个元素排列组合的情况时，可以使用这些库函数而不需要自己动手。

### 4.10. Iterating Over the Index-Value Pairs of a Sequence
#### problem
你想在迭代一个iterator对象的同时记录输出元素的位置信息。
#### solution

>The built-in enumerate() function handles this quite nicely:

```python
items=['a','b','c']
for no,val in enumerate(items,1):
    print(no,val)
#1,'a'
#2,'b'
#3,'c'
```
enumerate默认是从0开始对下表进行计数，也可以传入一个参数作为开始的值，如上面例子中使用了1作为开始基数。
这个方法可以非常方便的对一个文件内容进行逐行的分析并保存结果，比如统计文件中各个单词的出现的行数。
```python
words_summary=defaultdict(list)
with open('test.txt') as f:
	for idx,line in enumerate(f,1):
		words=[w.strip().lower() for w in line.split()]
		for word in words:
			words_summary[word].append(idx)
```
经过上述代码处理后，会得到以单词名称为键名，已列表为键值的字典，列表中保存的是对应单词在文本中出现的行数。
#### discussion
enumerate返回的仍然是iterator对象，但是这个iterator对象在迭代过程中返回的值是一个个tuple，由索引和值组成。
在使用enumerate时，应该注意，在for循环中要保留处理对象的格式，比如
```python
items=[('a','b'),('c','d'),('e','f')]
for idx,(a,b) in enumerate(items)#right!

for idx,a,b in enumerate(items)#error!
```