---
title: bs4模块运用总结
date: 2019-03-15 13:32:14
tags:
    - python
    - bs4
    - BeautifulSoup
categories:
    - 笔记总结
    - python模块
---

## 快速上手
```python
html = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>
<p class="story">...</p>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html, 'lxml')
print(soup.prettify())
print(soup.title.string)
```
<!--more-->
## 标签选择器
|属性|功能|使用eg|
|:-----:|:-----:|:-----:|
|title|选择元素|soup.title|
|head|选择元素|soup.head|
|p|选择元素|soup.p|
|name|获取名称|soup.title.name|
|attrs|获取属性|soup.p.attrs['name']或soup.p['name']|
|string|获取内容|soup.p.string或者soup.p.text|
|contents|获取子节点，返回list|soup.p.contents|
|children|获取子节点，返回迭代器|soup.p.children|
|descendants|获取子孙节点，返回迭代器|soup.p.descendants|
|parent|获取父节点,返回列表|soup.a.parent|
|parents|获取祖先节点,返回列表|soup.a.parents|
|next_siblings|获取后面的兄弟节点,返回列表|soup.a.next_siblings|
|previous_siblings|获取前面的兄弟节点,返回列表|soup.a.previous_siblings|

## 标准选择器
> find_all(name,attrs,recursive,text,**kwargs)

可根据标签名、属性、内容查找文档
```python
html='''
<div class="panel">
    <div class="panel-heading">
        <h4>Hello</h4>
    </div>
    <div class="panel-body">
        <ul class="list" id="list-1" name="elements">
            <li class="element">Foo</li>
            <li class="element">Bar</li>
            <li class="element">Jay</li>
        </ul>
        <ul class="list list-small" id="list-2">
            <li class="element">Foo</li>
            <li class="element">Bar</li>
        </ul>
    </div>
</div>
'''
from bs4 import BeautifulSoup
soup = BeautifulSoup(html, 'lxml')
print(soup.find_all(attrs={'id': 'list-1'}))
print(soup.find_all(attrs={'name': 'elements'}))
# 用法不同，结果相同
print(soup.find_all(id='list-1'))
print(soup.find_all(class_='element'))
print(soup.find_all(text='Foo')
```
> find( name , attrs , recursive , text , **kwargs )

find返回单个元素，find_all返回所有元素

```python
html="""
<div class="panel">
    <div class="panel-heading">
        <h4>Hello</h4>
    </div>
    <div class="panel-body">
        <ul class="list" id="list-1">
            <li class="element">Foo</li>
            <li class="element">Bar</li>
            <li class="element">Jay</li>
        </ul>
        <ul class="list list-small" id="list-2">
            <li class="element">Foo</li>
            <li class="element">Bar</li>
        </ul>
    </div>
</div>
"""

from bs4 import BeautifulSoup
soup = BeautifulSoup(html, 'lxml')
print(soup.find('ul'))
print(type(soup.find('ul')))
print(soup.find('page'))

>>>>>

<ul class="list" id="list-1">
<li class="element">Foo</li>
<li class="element">Bar</li>
<li class="element">Jay</li>
</ul>
<class 'bs4.element.Tag'>
None
```

|方法|描述|
|:-----:|:-----:|
|prettify|格式化html代码|
|find_parents()|返回所有祖先节点|
|find_parent()|返回直接父节点|
|find_next_siblings()|返回后面所有兄弟节点|
|find_next_sibling()|返回后面第一个兄弟节点|
|find_previous_siblings()|返回前面所有兄弟节点|
|find_previous_sibling()|返回前面第一个兄弟节点|
|find_all_next()|返回节点|后所有符合条件的节点|
|find_next()|返回第一个符合条件的节点|
|find_all_previous()|返回节点|后所有符合条件的节点|
|find_previous()|返回第一个符合条件的节点|

## css选择器
```python
html='''
<div class="panel">
    <div class="panel-heading">
        <h4>Hello</h4>
    </div>
    <div class="panel-body">
        <ul class="list" id="list-1">
            <li class="element">Foo</li>
            <li class="element">Bar</li>
            <li class="element">Jay</li>
        </ul>
        <ul class="list list-small" id="list-2">
            <li class="element">Foo</li>
            <li class="element">Bar</li>
        </ul>
    </div>
</div>
'''
from bs4 import BeautifulSoup
soup = BeautifulSoup(html, 'lxml')
print(soup.select('.panel .panel-heading'))
print(soup.select('ul li'))
print(soup.select('#list-2 .element'))
print(type(soup.select('ul')[0]))
for li in soup.select('li'):
    print(li.get_text())
```

## 总结
1. 推荐使用lxml解析库，必要时使用html.parser
2. 标签选择筛选功能弱但是速度快
3. 建议使用find()、find_all() 查询匹配单个结果或者多个结果
4. 如果对CSS选择器熟悉建议使用select()
5. 记住常用的获取属性和文本值的方法

## 参考文档
- [官方文档](https://beautifulsoup.readthedocs.io/zh_CN/latest/)
- [参考文章](https://www.jb51.net/article/65287.htm)
