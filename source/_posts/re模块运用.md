---
title: re模块运用
date: 2019-03-15 13:31:44
tags:
    - python
    - re
    - 正则表达式
categories:
    - 笔记总结
    - python模块
---

## 模块简介
在无规律的内容中，匹配到自己想要的内容
## 常见匹配模式
|模式 | 描述 |
|:----:|:----:|
|\w     |匹配字母数字及下划线|
|\W     |匹配非字母数字下划线|
|\s     |匹配任意空白字符，等价于 [\t\n\r\f].|
|\S     |匹配任意非空字符|
|\d     |匹配任意数字，等价于 [0-9]|
|\D     |匹配任意非数字|
|\A     |匹配字符串开始|
|\Z     |匹配字符串结束，如果是存在换行，只匹配到换行前的结束字符串|
|\z     |匹配字符串结束|
|\G     |匹配最后匹配完成的位置|
|\n     |匹配一个换行符|
|\t     |匹配一个制表符|
|^  |匹配字符串的开头|
|$  |匹配字符串的末尾。|
|.|匹配任意字符，除了换行符，当re.DOTALL标记被指定时，则可以匹配包括换行符的任意字符。|
|[...] |    用来表示一组字符,单独列出：[amk] 匹配 'a'，'m'或'k'|
|[^...]|不在[]中的字符：[^abc] 匹配除了a,b,c之外的字符。|
|*  |匹配0个或多个的表达式。|
|+  |匹配1个或多个的表达式。|
|?  |匹配0个或1个由前面的正则表达式定义的片段，非贪婪方式|
|{n}    |精确匹配n个前面表达式。|
|{n, m}     |匹配 n 到 m 次由前面的正则表达式定义的片段，贪婪方式|
|a\|b    |匹配a或b|
|( )    |匹配括号内的表达式，也表示一个组|
<!--more-->
## 函数分析
### match
re.match 尝试从字符串的`起始位置`匹配一个模式，如果不是起始位置匹配成功的话，match()就返回none,eg:r'http.*?baidu.com/(.*?).html'
```python
# group 为打印匹配结果,span为打印匹配长度
import re
content = '''Hello 1234567 World_This
is a Regex Demo
'''
result = re.match('^He.*?(\d+).*?Demo$', content, re.S)
print(result.group(1))
```
总结:
1. 尽量使用泛匹配(\s \w+)
2. 使用括号得到匹配目标
3. 尽量使用非贪婪模式(.*?)
4. 有换行符就用re.S
5. 有特殊字符需要匹配请用转义符号\
### 贪婪与非贪婪模式
```python
# 贪婪模式
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('^He.*(\d+).*Demo$', content)
print(result)
print(result.group(1))
>>>>> 7
# 非贪婪模式
import re

content = 'Hello 1234567 World_This is a Regex Demo'
result = re.match('^He.*?(\d+).*Demo$', content)
print(result)
print(result.group(1))
>>>>> 1234567
```
### search
re.search 扫描整个字符串并返回第一个成功的匹配
__为匹配方便，能用search就不用match__
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>'''
result = re.search('<li.*?singer="(.*?)">(.*?)</a>', html, re.S)
if result:
    print(result.group(1), result.group(2))
```
### findall
搜索字符串，以列表形式返回全部能匹配的子串
```python
import re

html = '''<div id="songs-list">
    <h2 class="title">经典老歌</h2>
    <p class="introduction">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href="/2.mp3" singer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" singer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="beyond">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" singer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" singer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>'''
results = re.findall('<li.*?href="(.*?)".*?singer="(.*?)">(.*?)</a>', html, re.S)
print(results)
print(type(results))
for result in results:
    print(result)
    print(result[0], result[1], result[2])

>>>>>
[('/2.mp3', '任贤齐', '沧海一声笑'), ('/3.mp3', '齐秦', '往事随风'), ('/4.mp3', 'beyond', '光辉岁月'), ('/5.mp3', '陈慧琳', '记事本'), ('/6.mp3', '邓丽君', '但愿人长久')]
<class 'list'>
('/2.mp3', '任贤齐', '沧海一声笑')
/2.mp3 任贤齐 沧海一声笑
('/3.mp3', '齐秦', '往事随风')
/3.mp3 齐秦 往事随风
('/4.mp3', 'beyond', '光辉岁月')
/4.mp3 beyond 光辉岁月
('/5.mp3', '陈慧琳', '记事本')
/5.mp3 陈慧琳 记事本
('/6.mp3', '邓丽君', '但愿人长久')
/6.mp3 邓丽君 但愿人长久
```
### sub
替换字符串中每一个匹配的子串后返回替换后的字符串,`精确匹配`
```python
import re
## 可以用原生匹配的内容 用\1表示 再加上r转义
content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra stings'
content = re.sub('(\d+)', r'\1 8910', content)
print(content)
```

### compile
将一个正则表达式串编译成正则对象，以便于**复用**该匹配模式
```python
import re

content = '''Hello 1234567 World_This
is a Regex Demo'''
# 可以创建pattern 供后面复用，这两步等于 最后一步result
pattern = re.compile('Hello.*Demo', re.S)
result = re.match(pattern, content)

result = re.match('Hello.*Demo', content, re.S)

print(result)
```
## 常用匹配
1. 匹配中文
    ```python
    [\u4e00-\u9fa5]
    ```
2. 匹配email地址
    ```python
    [\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?
    ```
3. 匹配身份证号码
    ```python
    ^(\d{6})(\d{4})(\d{2})(\d{2})(\d{3})([0-9]|X)$
    ```
4. 匹配国内电话号码
    ```python
    \d{3}-\d{8}|\d{4}-\{7,8}
    ```
5. 匹配年月日
    ```python
    ([0-9]{3}[1-9]|[0-9]{2}[1-9][0-9]{1}|[0-9]{1}[1-9][0-9]{2}|[1-9][0-9]{3})-(((0[13578]|1[02])-(0[1-9]|[12][0-9]|3[01]))|((0[469]|11)-(0[1-9]|[12][0-9]|30))|(02-(0[1-9]|[1][0-9]|2[0-8])))
    ```
6. 匹配网址
    ```python
    [a-zA-z]+://[^\s]*
    ```

## 其他
[在线正则测试](http://tool.oschina.net/regex#)
[正则表达式手册](http://tool.oschina.net/uploads/apidocs/jquery/regexp.html)
[正则测试在线程序](https://regex101.com/)