---
title: python.cookbook读书笔记三
date: 2017-10-10
categories: 
  - python
tags: 
  - python
  - 读书笔记
---
前面五小节中，我们学习了使用`defaultdict(list/set)`来初始化可以一个键保存多个值的字典、使用`OrderedDict`来创建在读取时能够保持插入顺序的字典、使用`zip`处理键值对以方便对字典进行简单地计算（min、max、sorted）以及对dict.keys()和dict.items()进行集合运算（&/-）和利用set和generator在保持字典内容顺序的同时删除字典中特定部分重复的内容。
<!-- more -->

#### 1.11 为切片命名
在对iteratable的结构进行切片操作时，我们常常会重复某些切片行为，在具体的场景中，我们可以对这些切片行为进行命名，示例如下：
```python
itema=[1,2,3,4,5]
itemb='ilovepython'
a=slice(2,4)
b=slice(2,5,2)
print(itema[a],'\n',itema[b],'\n',itemb[a],'\n',itemb[b])
del itema[a]
print('\n',itema) #[1,2,5]
#[3,4]
#[3,5]
#ov
#oe
```
在对切片命名时，实际上生成了一个切片对象
```python
a=slice(2,6,2)
print(a.start) #2
print(a.stop) #6
print(a.step) #2
```
为了防止索引值溢出的情况，可以使用`indices`方法对切片对象的切片效果进行修改
```python
a=slice(2,100,3)
items='helloworld'
print(a.indices(len(items)))
for i in range(*a.indices(len(items))):
    print(i,items[i],',')
# (2,10,2)
# 2l,5w,8l
```
`indices`方法会根据传入的值来得到恰当的切片参数，进而可以适应不同情况的切片任务。

#### 1.12 寻找序列中出现次数最多的元素

collections.Counter是一个被设计用来解决此类问题的类，将想要处理的list传入类中，将得到一个对象，这个对象可以调用most_common方法，用于返回出现次数最多的几个值。
```python
words=[
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around', 'the',
    'eyes', "don't", 'look', 'around', 'the', 'eyes', 'look', 'into',
    'my', 'eyes', "you're", 'under'
]
from collections import Counter
word_counts = Counter(words)
top_thress = word_counts.most_common(3) #得到出现次数最多的3个元素
```
使用word_counts[word]可以得到上一次统计结果中word的出现次数。
Counter对象还有`update`方法，该方法以一个list组为参数，可以更新其统计的数量信息。
鲜为人知的一点是，Counter对象可以进行集合运算，操作符为+，-，如：
```python
a=Counter(words)
b=Counter(morewords)
c=a+b
d=a-b
```
Counter对象打印出来的内部结构为`{item1:count1,item2,count2,...}`，按照从大到小的顺序排列，在任何需要统计数据的场景下，Counter都是一个出色的工具函数。

#### 1.13 根据共同的key来对一系列dict值进行排序
操作符模块的`itemgetter`方法非常适合解决这样的问题，该方法可传入一个或者多个键名，返回键名对应的键值，如下所示：
```python
rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1002},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]
from operator import itemgetter
rows_by_fname=sorted(rows,key=itemgetter("fname"))
#sorted by fname
rows_by_uid=sorted(rows,key=itemgetter("uid"))
#sorted by uid
rows_by_lfname=sorted(rows,key=itemgetter("lname","fname"))
#first sorted by lname , then sorted by fname
```
在不使用`itemgetter`的情况下，可以用lambda来代替
```python
rows_by_fname=sorted(rows,key=lambda r:r['fname'])
rows_by_llfname=sorted(rows,key=lambda r:(r['lname'],r['fname']))
```
itemgetter同样可以用于`min`和`max`方法中：
```python
rows_min=min(rows,key=itemgetter('uid'))
rows_max=max(rows,key=itemgetter('uid'))
```

#### 1.14 根据某种性质来比较原生不支持比较操作的对象
比如当你有一系列的实例化的类，你想要根据每一个实例的某个性质来对这些实例进行排序，但这些实例原生是不支持排序方法的，因此你需要在sorted函数中传入处理函数以得到排序标准，这里同样可以使用lambda来解决这类问题，但是，无论是itemgetter还是本节所要讲的attrgetter，他们都要比lambda的执行效率高一些，因此，在注重性能的情况下，优先使用operator库中的函数。
```python
class User:
    def __init__(self,user_id):
        self.user_id=user_id
    def __repr__(self):
        return 'User({})'.format(self.user_id)
users=[User(1),User(4),User(3),User(2)]
sorted_users1=sorted(users,key=lambda u:u.user_id)
sorted_users2=sorted(users,key=attrgetter('user_id'))
```
上面代码中两种写法得到的结果是一样的，但是使用attrgetter会更快一些。同`itemgetter`一样，`attrgetter`也可以用于min、max方法中并同样可以传入多个参数作为索引。
#### 1.15对字典序列根据特定键值进行分组
当你得到了由一系列结构相似的dict组成的序列，你想要将这些数据按照某种规则进行分组，此时可以使用`itertools.groupby()`方法，
```python
rows = [
    {'address': '5412 N CLARK', 'date': '07/01/2012'},
    {'address': '5148 N CLARK', 'date': '07/04/2012'},
    {'address': '5800 E 58TH', 'date': '07/02/2012'},
    {'address': '2122 N CLARK', 'date': '07/03/2012'},
    {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'},
    {'address': '1060 W ADDISON', 'date': '07/02/2012'},
    {'address': '4801 N BROADWAY', 'date': '07/01/2012'},
    {'address': '1039 W GRANVILLE', 'date': '07/04/2012'},
]
from operator import itemgetter
from itertools import groupby
rows.sorted(key=itemgetter('date'))
grouped_rows=groupby(rows,key=itemgetter('date'))
```
上述代码得到的结果，是以日期为键名，以相应分组结果组成的list为键值的序列。<b>由于groupby只能会对相邻的数据进行同类检验，因此必须先对source进行排序，不然可能得不到你想要的结果。</b>你可能会忍不住跳起来说，何必这么dirty，只要使用defaultdict再来个循环就搞定了：
```python
from collections import defaultdict
rows_by_date=default(list)
for row in rows:
    rows_by_date[row['date']].append(row)
```
这种方式当然是可以的，而且省去了排序的步骤，但是由于defaultdict在内存方面消耗会较大，因此不建议用来处理大批量的数据，当然，在无需关心内存的情况下，上述代码要比排序后再使用groupby要来的快一些。
