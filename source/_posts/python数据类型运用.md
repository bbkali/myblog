---
title: python数据类型运用
date: 2019-03-20 09:33:49
tags:
    - python 
categories:
    - 笔记总结
    - 数据类型
---

Python共有8种数据类型

|类型|备注|
|:----:|:----:|
|Number(数字)|不可变值|
|String(字符串)|不可变值|
|Boolean(布尔值)|不可变值|
|None(空值)|不可变值|
|List(列表)|可变值|
|Tuple(元组)|不可变值|
|dict(字典)|可变值|
|set(集合)|可变值|

<!-- more -->
## 类型判断
判断某个值属于那种数据类型
### isinstance
isinstance(x, (A,B,……))
x ：需要判断的变量
(A,B,……):备选的变量类型，可以是一个元素，也可以是一个元组。如果是元素，只要变量类型与元组中任意一个匹配，返回值即为真。
```python
b = [1,2,3,4,5]
print(isinstance(b,(int,str)))
#result:False
print(isinstance(b,(int,str,list)))
#result:True
```

### type
```python
In [29]:  type(dataset)
Out[29]: list
```
### 区别
isinstance() 与 type() 区别：
1. type() 不会认为子类是一种父类类型，不考虑继承关系。
2. isinstance() 会认为子类是一种父类类型，考虑继承关系。
3. 如果要判断两个类型是否相同推荐使用 isinstance()

## 类型转换总结
|函数|描述|
|:-----:|:-----:|
|int(x[,base])|将x转换为一个整数|
|float(x)|将x转换到一个浮点数|
|complex(real[,imag])|创建一个复数|
|str(x)|将对象x转换为字符串|
|repr(x)将对象x转换为表达式字符串|
|eval(str)|用来计算在字符串中的有效Python表达式,并返回一个对象|
|tuple(s)|将序列s转换为一个元组|
|list(s)|将序列s转换为一个列表|
|set(s)|转换为可变集合|
|dict(d)|创建一个字典。d必须是一个序列(key,value)元组。|
|frozenset(s)|转换为不可变集合|
|chr(x)|将一个整数转换为一个字符|
|ord(x)|将一个字符转换为它的整数值|
|hex(x)|将一个整数转换为一个十六进制字符串|
|oct(x)|将一个整数转换为一个八进制字符串|

## 类型运用
### Number(数字)
#### int 
```python
int转换为数字类型,直接舍弃浮点数

int(1.9)
int(1.1)
>>>>>　１
```
#### float
float函数可将x转化成浮点数
```python
float(1)
>>>>> 1.0
```
#### complex
complex(a,b)
a,b表示复数的实部和虚部，且都是浮点数

#### 数学函数
abs(x) 返回数字的绝对值
pow(x,y) 求x的y次方值
max() 返回给定参数求最大值
min() 返回给定参数的最小值
round(x,n) 四舍五入到n位数,n为可选参数
pi  数学常量 pi（圆周率，一般以π来表示）
e   数学常量 e，e即自然常数（自然常数）

### String(字符串)
|方法|描述|
|:----:|:----:|
|string.capitalize()|把字符串的第一个字符大写|
|string.center(width)|返回一个原字符串居中,并使用空格填充至长度 width 的新字符串|
|string.count(str,beg=0,end=len(string))|返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数|
|string.decode(encoding='UTF-8', errors='strict')|以 encoding 指定的编码格式解码 string，如果出错默认报一个 ValueError 的 异 常 ， 除非 errors 指 定 的 是 'ignore' 或 者'replace'|
|string.endswith(obj, beg=0, end=len(string))|检查字符串是否以 obj 结束，如果beg 或者 end 指定则检查指定的范围内是否以 obj 结束，如果是，返回 True,否则返回 False.|
|string.find(str, beg=0, end=len(string))|检测 str 是否包含在 string 中，如果 beg 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1|
|string.format()|格式化字符串|
|string.index(str, beg=0, end=len(string))|跟find()方法一样，只不过如果str不在 string中会报一个异常|
|string.isalnum()|如果 string 至少有一个字符并且所有字符都是字母或数字则返
回 True,否则返回 False|
|string.isalpha()|如果 string 至少有一个字符并且所有字符都是字母则返回 True,
否则返回 False|
|string.isdigit()|如果 string 只包含数字则返回 True 否则返回 False|
|string.islower()|如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回 True，否则返回 False|
|string.join(seq)|以 string 作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串|
|string.ljust(width)|返回一个原字符串左对齐,并使用空格填充至长度 width 的新字符串|
|string.lower()|转换 string 中所有大写字符为小写|
|string.lstrip()|截掉 string 左边的空格|
|max(str)|返回字符串 str 中最大的字母|
|min(str)|返回字符串 str 中最小的字母|
|string.replace(str1, str2,  num=string.count(str1))|把 string 中的 str1 替换成 str2,如果 num 指定，则替换不超过 num 次|
|string.rfind(str, beg=0,end=len(string) )|类似于 find()函数，不过是从右边开始查找|
|string.strip([obj])|在 string 上执行 lstrip()和 rstrip()|
|string.title()|返回"标题化"的 string,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())|
|string.upper()|转换 string 中的小写字母为大写|
|string.strip([obj])|在 string 上执行 lstrip()和 rstrip()|
### Boolean(布尔值)
### None(空值)
### List(列表)
#### 更新列表
```python
list1 = []          ## 空列表
list1.append('test')   ## 使用 append() 添加元素
```
#### 删除列表
```python
list1.remove('test')
```
#### 列表截取
```python
>>>L = ['Google', 'Runoob', 'Taobao']
>>> L[2]
'Taobao'
>>> L[-2]
'Runoob'
>>> L[1:]
['Runoob', 'Taobao']
>>>
```
#### 列表常用方法
1. list.count(obj) 统计某个元素在列表中出现的次数
2. list.extend(seq) 在列表末尾一次性追加另一个序列中的多个值
3. list.insert(index,obj) 将对象插入列表
4. list.pop(index=-1) 移除列表中的一个元素（默认最后一个元素），并且返回该元素的值
5. list.reverse() 反向列表中的元素
6. list.sort(cmp=None, key=None, reverse=False) 对原列表进行排序 或者a= sorted(list) a为排列好的列表

### Tuple(元组)
只需要在括号中添加元素，并使用逗号隔开即可
1. 创建 tup1 = (50,)
2. 可以用索引拼接，不可单独修改
3. 列表转换成元组用tuple(seq)
### dict(字典)
字典的每个键值 key=>value 对用冒号 : 分割，每个键值对之间用逗号 , 分割，整个字典包括在花括号 {} 中 ,格式如下所示：
```python
d = {key1 : value1, key2 : value2 }
```
#### 修改字典
```python
#!/usr/bin/python
 
dict1 = {'Name': 'Zara', 'Age': 7, 'Class': 'First'}
 
dict1['Age'] = 8 # 更新
dict1['School'] = "2321" # 添加
del dict1['Name']  # 删除键是'Name'的条目
```
#### 常用方法
1. dict.clear() 删除字典内所有元素 
2. dict.copy() 返回一个字典的浅复制
3. dict.fromkeys(seq[, val]) 创建一个新字典，以序列 seq 中元素做字典的键，val 为字典所有键对应的初始值
4. dict.get(key, default=None) 返回指定键的值，如果值不在字典中返回default值
5. dict.has_key(key) 如果键在字典dict里返回true，否则返回false
6. dict.items() 以列表返回可遍历的(键, 值) 元组数组
7. dict.keys() 以列表返回一个字典所有的键
8. dict.values() 以列表返回字典中的所有值
9. dict.setdefault(key, default=None) 和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default
10. dict.update(dict2) 把字典dict2的键/值对更新到dict里
11. pop(key[,default]) 删除字典给定键 key 所对应的值，返回值为被删除的值。key值必须给出。 否则，返回default值
12. popitem() 随机返回并删除字典中的一对键和值

### set(集合)
类似dict，是一组key的集合，不存储value,本质是无序无重复的元素集合
#### 常用用法
```python
list1=[1,2,3,4,5,1]
## 去重
s1 =set(list1)
## 删除
s1.remove(1)
## 添加
s1.add(1)
## 交集
s1 & s2
## 并集
s1 | s2
## 差集
>>> s1
{2, 33, 13, 23}
>>> s2
{1, 2, 22, 31}
>>> s2 - s1
{1, 22, 31}
>>> s1 - s2
{33, 13, 23}
```

