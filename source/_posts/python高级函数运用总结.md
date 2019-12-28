---
title: python高级函数运用总结
date: 2019-03-20 09:33:28
tags:
    - python
categories:
    - 笔记总结
    - 高级函数
---

## map
map()函数接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回
```python
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]

------

>>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
['1', '2', '3', '4', '5', '6', '7', '8', '9']
```
<!-- more -->

## reduce
`reduce`把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是：
> reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

```python
>>> from functools import reduce
>>> def fn(x, y):
...     return x * 10 + y
...
>>> reduce(fn, [1, 3, 5, 7, 9])
13579
```
可以利用map + reduce 写出一个与int函数类似的功能或者float函数
```python
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
    return DIGITS[s]

def str2int(s):
    return reduce(lambda x, y: x * 10 + y, map(char2num, s))


------------------------------------

from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
    return DIGITS[s]

def str2int(s):
    inb=s.split('.')[0]
    fb=s.split('.')[-1]
    return reduce(lambda x, y: x * 10 + y, map(char2num, inb))+reduce(lambda x, y: x*0.1 + y*0.01, map(char2num, fb))

a=str2int('123.13')
print(a)
```

## filter
和map()类似，filter()也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素
```python
# 例如，在一个list中，删掉偶数，只保留奇数，可以这么写：
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

## sorted
`sorted()`函数也是一个高阶函数，它还可以接收一个key函数来实现自定义的排序，例如按绝对值大小排序
```python
>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
----------------------------------------
我们给sorted传入key函数，reverse为反向，即可实现忽略大小写的排序：
```python
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
['Zoo', 'Credit', 'bob', 'about']
```

## lambda
关键字lambda表示匿名函数，冒号前面的x表示函数参数，用匿名函数有个好处，因为函数没有名字，不必担心函数名冲突
```python
# 如下用匿名函数表示20内奇数
>>> list(filter(lambda x : x%2>0 ,range(20)))
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19]
```

## 偏函数
总结functools.partial的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单
```python
>>> import functools
>>> int2 = functools.partial(int, base=2)
>>> int2('1000000')
64
>>> int2('1010101')
85

## 实际上固定了int()函数的关键字参数base,相当于
kw = { 'base': 2 }
int('10010', **kw)
```