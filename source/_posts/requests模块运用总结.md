---
title: requests模块运用总结
date: 2019-03-15 13:32:01
tags:
    - python
    - requests
categories:
    - 笔记总结
    - python模块
---
## 快速上手
```python
import requests
url='https://www.baidu.com'
# 使用get请求访问
r=requests.get(url,headers=None,proxy=None,allow_redirects=False,timeout=None)
```
|属性|描述|返回格式|
|:----:|:----:|
|r.text|响应内容|str|
|r.content|二进制响应内容|str|
|r.cookies|发送的cookies|dict(cookies_are='working')|
|r.url|访问的url|url|
|r.header|请求头|header={'useragent':'firefox'}|
|r.status_code|访问状态200|2XX,4XX,5XX|
|r.request.header|返回头|字典格式|
|r.encoding|编码模式|utf-8|
|r.json|返回的json内容|字典格式|
<!--more-->
```python
>>> r.request.headers
{'Accept-Encoding': 'identity, deflate, compress, gzip',
'Accept': '*/*', 'User-Agent': 'python-requests/0.13.1'}

>>> r.headers
{'content-length': '56170', 'x-content-type-options': 'nosniff', 'x-cache':
'HIT from cp1006.eqiad.wmnet, MISS from cp1010.eqiad.wmnet', 'content-encoding':
'gzip', 'age': '3080', 'content-language': 'en', 'vary': 'Accept-Encoding,Cookie',
'server': 'Apache', 'last-modified': 'Wed, 13 Jun 2012 01:33:50 GMT',
'connection': 'close', 'cache-control': 'private, s-maxage=0, max-age=0,
must-revalidate', 'date': 'Thu, 14 Jun 2012 12:59:39 GMT', 'content-type':
'text/html; charset=UTF-8', 'x-cache-lookup': 'HIT from cp1006.eqiad.wmnet:3128,
MISS from cp1010.eqiad.wmnet:80'}
```
## 访问函数
### Get
```python
"""
header为请求头,proxy为代理
allow_redirects为是否设置重定向
params为拼接url的参数,cookies 为加入的cookies
timeout为访问超时的时间，超时将报错
"""
r=requests.get(url,headers=None,proxies=None,allow_redirects=False,params=None,cookies=cookies,timeout=None)
```

### Post
```python
# data为post传入参数，字典格式
data={'username':'admin','passwd':'123456'}
r= requests.post(url,data=data)
```

## Ression
r =requests.Ression()
r.get()
r.post()

## 参考文献
[官方文档](http://cn.python-requests.org/zh_CN/latest/)

