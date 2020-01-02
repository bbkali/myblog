---
title: 代码编写writeup
date: 2019-03-12 11:51:49
tags:
    - CTF
categories:
    - CTF
    - writeup
---
## 第一题：md5('root')
![md5root](https://upload-images.jianshu.io/upload_images/4838174-a9d10ce2aa69377b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码如下:
```python
import hashlib
time=0
m1='root'
while time <500:
    time+=1
    m1=hashlib.md5(m1.encode('utf-8')).hexdigest()
print(m1)

```
<!--more-->
## 第二题：秋名山的老司机
![秋名山的老司机](https://upload-images.jianshu.io/upload_images/4838174-deaed8cc0137e50a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码如下:
```python
import requests
import re

ret=requests.session()
url="http://192.168.1.88/CODE02/"
res=ret.get(url)
number=re.findall('<br/>(.*?)</p>',res.text,flags=re.DOTALL)[0].replace('\n','')
final=eval(number)
result={'result':final}
res=ret.post(url,data=result)
print(res.text)
```
## 第三题：3秒
![1524123983(1).png](https://upload-images.jianshu.io/upload_images/4838174-ddcbd0ea8c5e2e6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码如下:
```python
import requests
import re
ret=requests.session()
url="http://192.168.1.88/CODE03/"
res=ret.get(url)
a=int(re.findall('<br/>a=(.*?)<br/>',res.text,flags=re.DOTALL)[0].replace('\n',''))
b=int(re.findall('<br/>b=(.*?)<br/>',res.text,flags=re.DOTALL)[0].replace('\n',''))
c=int(re.findall('<br/>c=(.*?)<br/>',res.text,flags=re.DOTALL)[0].replace('\n',''))
d=int(re.findall('<br/>d=(.*?)<br/>',res.text,flags=re.DOTALL)[0].replace('\n',''))
ss=re.findall('the answer is:(.*?)<br/>',res.text,flags=re.DOTALL)[0].replace('\n','')
final=a+b+(c*2)-d
payload={'answer':final}
res=ret.post(url,data=payload)
print(res.text)
```
## 第四题：提交就有flag
![提交就有flag](https://upload-images.jianshu.io/upload_images/4838174-d9d1be4737550b09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>题目逻辑：通过访问页面获取get_flag参数，解密base64后post传入即可得flag

代码如下:
```
import requests
import base64
url="http://192.168.1.88/CODE04/"
ret=requests.session()
res=ret.get(url)
final=(res.headers['Get-flag'])
final=base64.b64decode(final).decode('utf')
payload={'SniperOJ':final}
#print(payload)
res=ret.post(url,data=payload)
print(res.text)
```




