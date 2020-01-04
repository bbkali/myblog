---
title: Sqlmap注入使用技巧
date: 2019-04-09 13:13:05
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

## 常用检测参数
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


## 参考文章
[官方地址](http://sqlmap.org)
[Github地址](https://github.com/sqlmapproject/sqlmap)
[中文使用说明](http://www.91ri.org/6775.html)
[sqlmap使用笔记](https://www.cnblogs.com/coder2012/p/3939703.html)
[sqlmap注入教程](https://www.cnblogs.com/ichunqiu/p/5805108.html)