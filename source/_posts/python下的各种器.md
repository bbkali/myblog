---
title: python下的各种器
date: 2019-03-24 11:52:30
tags:
    - python
    - 生成器
    - 迭代器
    - 装饰器
categories:
    - 笔记总结
    - 器
---

## 生成器(generator)
在Python中，这种一边循环一边计算的机制，称为`生成器：generator`，generator保存的是`算法`
### 创建 generator：
```python
# 方法一 ： 
>>> g = (x * x for x in range(5))
>>> g
>>> <generator object <genexpr> at 0x1022ef630>
>>> >>> next(g)
0
>>> next(g)
1
>>> next(g)
4
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
## 为避免报错 建议使用for循环
-------------------------
# 方法二 yield: 如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator，yield为下次继续在该位置继续执行

# 以斐波那契数列为例子
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
# yield为下次继续在该位置继续执行
```
<!-- more -->

## 迭代器(Iterator)
凡是可作用于for循环的对象都是Iterable类型；
凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列
集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象.
生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。
把list、dict、str等Iterable变成Iterator可以使用iter()函数
```python
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True
```
## 装饰器(decorator)
简单用法如下:
```python
# *arg,**kw 表示所有任意参数
def test(func):
    def printname(*arg,**kw):
        print('this is 装饰器')
        return func(*arg,**kw)
    return printname

@test
def ceshi():
    print('我是ceshi函数')

ceshi()

>>>>
this is 装饰器
我是ceshi函数

```
详细请看另一篇[文章](https://bbkali.github.io/2019/03/19/python%E4%B8%8B%E8%A3%85%E9%A5%B0%E5%99%A8%E7%9A%84%E8%AF%A6%E8%A7%A3/)