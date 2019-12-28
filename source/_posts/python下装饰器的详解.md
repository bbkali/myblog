---
title: python下装饰器的详解
date: 2019-03-19 17:30:13
tags:
    - python
    - 装饰器
categories:
    - 笔记总结
    - 装饰器
---

# 装饰器简介
---
实质： 是一个函数
参数：是你要装饰的函数名（并非函数调用）
返回：是装饰完的函数名（也非函数调用）
作用：为已经存在的对象添加额外的功能
特点：不需要对对象做任何的代码上的变动
应用场景：插入日志、性能测试、事务处理、权限校验等

---

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
<!--more-->

# 原理
把`@test`放到`ceshi()`函数的定义处，相当于执行了语句
ceshi = test(ceshi)=printname
所以 ceshi()=printname()

## 高阶装饰器
### 带参数
```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print('2019-3-20')


>>> now()
execute now():
2019-3-20
```
和两层不同的是三层的执行顺序如下

> now = log('execute')(now)

参数是now函数，返回值最终是wrapper函数,函数也是对象，它有__name__等属性,它们的__name__已经从原来的'now'变成了'wrapper
```python
>>> now.__name__
'wrapper'
```
Python内置的functools.wraps就是干这个事的，所以，一个完整的decorator的写法如下
```python
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

## 实践案例
请设计一个decorator，它可作用于任何函数上，并打印该函数的执行时间：
```python
import time
import functools


def ddq(text):
    def say(func):
        @functools.wraps(func)
        def deco(*args, **kw):
            print(text, func.__name__, time.ctime())
            return func(*args, **kw)
        return deco
    return say


@ddq('ed')
def test():
    print('this is test')


>>>> test()
ed test Tue Mar 19 18:11:09 2019
this is test
```