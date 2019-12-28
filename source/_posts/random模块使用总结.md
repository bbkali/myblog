---
title: random模块使用总结
date: 2019-03-11 16:19:02
tags:
    - python
    - random
categories:
    - 笔记总结
    - python模块
---

## 随机整数,下限必须小于上限
```python
>>> import random
>>> random.randint(0,99)
21
```
## 随机选取0到100的偶数
```python
>>> import random
>>> random.randrange(0, 101, 2)
```
<!-- more -->
## 随机字符
```python
>>> import random
>>> random.choice('abcdefg&#%^*f')
'd'

>>> import random
>>> random.choice ( ['apple', 'pear', 'peach', 'orange', 'lemon'] )
'lemon'
```

## 随机浮点数
```python
>>> import random
>>> random.random() 
0.85415370477785668
>>> random.uniform(1, 10)
5.4221167969800881
```

## 选取特定数量的字符
```python
>>> import random
random.sample('abcdefghij',3) 
['a', 'd', 'b']
```

## 打乱排序
```python
>>> import random
>>> items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0]
>>> random.shuffle(items)
>>> items
[1, 9, 7, 6, 2, 5, 0, 4, 8, 3]
```