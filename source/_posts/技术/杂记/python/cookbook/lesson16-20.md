---
title: python.cookbook读书笔记四
date: 2017-10-23
categories: 
  - python
tags: 
  - python
  - 读书笔记
---

在11-15小节中，讲述了为切片命名的技巧，使用counter类来统计list中各个元素的数量，以及itemgetter用于在对字典排序时类目的获取、attrgetter用于对对象排序时属性的获取，最后是使用groupby对排序后的list内容进行分组。接下来是这一章的最后五小节。
<!-- more -->
#### filter、compress实现对list的筛选
当然，在数据量非常小的情况下，我们使用list comprehension来实现筛选
```shell
>>> mylist = [1, 4, -5, 10, -7, 2, 3, -1]
>>> [n for n in mylist if n > 0]
[1, 4, 10, 2, 3]
>>> [n for n in mylist if n < 0]
[-5, -7, -1]
>>> 
```
潜在的隐患就是当数据量较大是可能会一次生成太多的数据，这时可以使用iterator来逐次返回需要的数据
```shell
>>> pos = (n for n in mylist if n > 0)
>>> pos
<generator object <genexpr> at 0x1006a0eb0>
>>> for x in pos:
... print(x)

```
有时，list comprehension难以表达某些复杂的筛选条件，这是filter就派上用场了，使用filter初始化后的函数可以用于生成新的筛选后的列表
```python
values=['1','2','-3','-','4','N/A','5']
def is_int(val):
    try:
        x=int(val)
        return True
    except ValueError:
        return False
filtedvalues=list(filter(is_int,values))
print(filtedvalues)# ['1', '2', '-3', '4', '5']
```
filter同样是生成的iterator,因此想要得到结果仍需要使用list()来将其变成真正的list.
##### discussion
list comprehension和generator是两种最直接简单的筛选列表的方式，其中list comprehension是即时生成的，甚至你可以选择替代某些不符合筛选要求的元素为某种东西。
```python
replaced=[n if n>0 else 0 for n in values]
#如果values中存在小于0的则将其作为0返回
```
另外还有一种compress方法可以将筛选条件以值为boolean的list的形式传入，则会选出相应位置为true的元素。
```python
counts=[3,4,5,6,7,8]
from itertools import compress
more5=[n>5 for n in counts]
print(list(compress(address,more5)))
#将address中相应位置在counts中大于5的位置的元素选出来。
```
#### 1.17筛选字典元素
使用dict comprehension同样也是十分方便的处理该问题的方法：
```python
prices={
    'ACME': 45.23,
    'AAPL': 612.78,
    'IBM': 205.55,
    'HPQ': 37.20,
    'FB': 10.75
}
# Make a dictionary of all prices over 200
p1 = {key:value for key,value in prices.items() if value>200}
# Make a dictionary of tech stocks
tech_names = { 'AAPL', 'IBM', 'HPQ', 'MSFT' }
p2 = {key:value for key,value in prices.items() if key in tech_names}
```
也可以传入一系列的tuple来生成，但是这样的做法会比较慢
```python
p1=dict((key,value) for key,value in prices.items() if value>200)
p2={key:prices(key) for key in prices.keys()&tech_names}
#这种做法也会比较慢
```

#### 1.18 namedtuples
当你写了一段代码，通过位置来获取列表或者元祖中的元素，你会发现这段代码在理解上会存在问题，而且也会存在隐患，比如当你获取到的数据源发生变化时，你可能会到处找这样的代码来修改。
collections.namedtuples是一个将位置命名并返回新的数据结构的类，在处理上述情况时非常有用。
```shell
>>>from collections import namedtuple
>>>Subscriber=namedtuple('Subscriber',['addr','joined'])
>>>sub=Subscriber('xc93224@yeah.net','20171023')
>>>sub
Subscriber(addr='xc93224@yeah.net',joined='20171023')
```
实例化的namedtuple通过tuple来保存信息，并支持大多数tuple的操作
```shell
>>>len(sub)
2
>>>addr,joined=sub
>>>addr
'xc93224@yeah.net'
```
多数情况下namedtuple被用来解决信息获取与位置的耦合关系。
```python
from collections import namedtuple
#假设你从数据库获取到了一系列股票的信息,你想要算出总价
Stock=namedtuple('Stock',['name','shares','price'])
def compute_cost(stocks):
    total=0
    for stock in stocks:
        s=Stock(*stock)
        total+=s.shares*s.price
    return total
```
##### discussion
可能你会问了，如果我传入namedtuple实例的元素数量不够怎么办呢，这就要提到`_replace`方法，该方法接收字典类型的参数，可以将字典中对应的值与namedtuple中的值进行替换。
```python
from collections import namedtuple
Stock=namedtuple('Stock',['name','shares','price','date','time'])
stock_prototype=Stock('',0,0,None,None)
def dict_to_stock(s):
    return stock_prototype._replace(**s)
```
如果你只是需要一种能够高效地改变属性的数据结构，那么namedtuple并不是最好的选择，在类中定义`__slots__`方法来实现是更好的选择。

#### 1.19 得到结果的同时进行转换和筛选
##### problem
你需要对一系列数据进行某个reduction操作(eg.,sum()、min()、max())，但在得到结果之前需要对数据进行转换或者筛选。
##### solution
一个非常elegent的方法是使用generator-expression，例如
```shell
>>>nums=[1,2,3]
>>>sum(x**2 for x in nums)
14
```
其他几个例子：
```python
#判断某个目录下存在py文件
import os
files=os.listdir('dirname')
if any(name.endswith('.py') for name in files):
    print("There be python")
else:
    print("Sorry,no python")

#使用分隔符连接元素得到字符串
s=('ACME',25,30)
print(','.join(str(x) for x in s))

#对数据结构进行reduction操作

portfolio = [
    {'name':'GOOG', 'shares': 50},
    {'name':'YHOO', 'shares': 75},
    {'name':'AOL', 'shares': 20},
    {'name':'SCOX', 'shares': 65}
]
min_shares=min(s['shares'] for s in portfolio)
```
##### discussion
###### 1.效率问题
在第一个例子中，`sum(x**2 for x in nums)`与`sum((x**2 for x in nums))`会得到相同的结果，事实上两者都是将generator-expr作为参数传进sum函数，但是前者语法上更加简洁，在此提醒，不推荐使用`sum([x**2 for x in nums])`这种方式，因为会占据额外的内存，这点在数据量比较大的时候会非常明显。
###### 2.key值
事实上reduction函数例如min、max会接收第二个参数作为key值，这可能在你想要使用generator时会有用。
```python
#最初的方式，直接得到数值
min_share=min(s['shares'] for s in portfolio)
#增加key，得到{'name':'AOL','shares':20},即整条信息
min_share=min(portfolio,key=lambda s:s['shares'])
```

#### 1.20 将多个映射表连接成一个

##### problem
你想要将多个映射表集中在一起进行某个操作，比如查询和筛选等。
##### solution
`from collections import ChainMap`
假设你有两个dict:
`a={'x':1,'z':3}`
`b={'y':2,'z':4}`
现在你想要从这两个字典中查找某个键值，（先从a找，若没有，再从b找），最简单的方法是使用ChainMap：
```python
from collectiosn import ChainMap
c=ChainMap(a,b)
print(c['x']) #1
print(c['y']) #2
print(c['z']) #3
```
##### discussion
虽然ChainMap使得多个字典看起来像是在同一序列中，但其实并没有真正将这些字典合并，而是将这些字典的引用在内部保存为一个list，并重新定义了接口使得可以通过这个list来访问这些字典。大多数的字典操作都是有效的：
```shell
>>>len(c)
3
>>>list(c.keys())
['x','y','z']
>>>list(c.values())
[1,2,3]
```
如果同一个chainmap中的字典中相互之间有重复的键名，那么通过chainmap只能访问到第一次出现的哪个值。
ChainMap在操作程序中不同作用域的参数时，非常有用：
```shell
>>>values=ChainMap()
>>>values['x']=1
>>>#添加新的mapping
>>>values=values.new_child()
>>>values['x']=2
>>>values=values.new_child()
>>>values[x]=3
>>>values
ChainMap({'x':3},{'x':2},{'x':1})
>>>values['x']
3
>>>#去掉最近添加的mapping
>>>values=values.parents
>>>values['x']
2
>>>values=values.parents
>>>values['x']
1
>>>values
ChainMap({'x':1})
```
可能你会说，不用chainmap也可以实现上述方法，那就是dict的update方法，比如：
```shell
>>>merged=dict(b)
>>>merged.update(a)
>>>list(merged['x'],merged['y'],merged['z'])
[1,2,3]
```
但是，这种方式要求使用两个不同的字典（才能保持访问的有效性），并且对原始dict的修改也无法反映到merged中。
```shell
>>>a['x']=3
>>>merged['x']
1
```
由于ChainMap访问的是原始dict，因此不会受上述限制
```shell
>>>merged=ChainMap(a,b)
>>>merged['x']
1
>>>a['x']=43
>>>merged['x']
43
```
