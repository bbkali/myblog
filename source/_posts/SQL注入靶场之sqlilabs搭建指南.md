---
 title: SQL注入靶场之sqlilabs搭建指南
 date: 2020-02-24 18:57:36
 tags: SQL注入
 categories: SQL注入
---

## SQLiLabs简介
据统计，互联网上SQL注入漏洞占整体的百分之六十左右，所以学好SQL注入的漏洞挖掘将十分重要。
![2020-2-24-19-45-6](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-24-19-45-6)
这次要使用的靶机环境是SQLi Labs，SQLi-Labs是一个专业的SQL注入练习平台，它是只专注SQL注入漏洞，以便我们研究学习SQL注入的整个注入过程，适用于GET和POST场景，包含了以下注入：
 
|类型|备注|
|:----:|:----:|
|基于错误的注入|字符和整数型|
|基于误差的注入|双查询注入|
|盲注入|布尔和时间型|
|更新查询注入|update |
|插入查询注入|insert |
|Header头部注入|（01、基于Referer注入， 02、基于UserAgent注入，03、基于cookie注入）|
|绕过WAF|绕过黑名单\过滤器\剥离\注释剥离 OR＆AND 剥离空格和注释剥离 UNION和SELECT|
|绕过特殊函数|addslashers、mysql_real_escape_string函数等|
|堆叠注入||
<!--more-->
## 安装
### 下载地址
SQLiLabs源码下载：https://github.com/Audi-1/sqli-labs
一体化环境搭建phpstudy: https://www.xp.cn/
phpStudy是一个PHP调试环境的程序集成包。恰好我们可以用到"PHP+Mysql+Apache"
### 配置环境
Tibs:务必将sql的版本调到5.5以上，因为这样你的数据库内才会有information_schema数据库，方便进行实验测试

1. 将之前下载的源码解压到ohpstudy安装目录下的www目录下，修改sql-connections/db-creds.inc文件当中的mysql账号密码！默认的mysql数据库地址是“127.0.0.1 或 localhost"，用户名和密码都是"root"。主要是修改’$dbpass参数并保存
```
<?php
//give your mysql connection username n password
$dbuser ='root';
$dbpass ='root';
$dbname ="security";
$host = 'localhost';
$dbname1 = "challenges";
```
2. 打开浏览器http://127.0.0.1/sqli-labs/”访问首页，并点击“Setup/reset Database”以创建数据库，创建表并填充数据

3. 向下翻，就可以看到有很多不同的注入点了，分为基本SQL注入、高级SQL注入、SQL堆叠注入、挑战四个部份，总共约75个SQL注入漏洞，一关关点击进入闯关，能够使用SQL语句执行数据库命令即可选择下一关，开始注入吧！
![2020-2-24-19-33-42](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-24-19-33-42)
