---
title: pymysql模块使用总结
date: 2019-03-11 16:19:26
tags:
    - python
    - pymysql
categories:
    - 笔记总结
    - pymysql
---

## 基本使用
```python
# 导入pymysql模块
import pymysql
# 连接database
conn = pymysql.connect(host=“你的数据库地址”,user=“用户名”
    ,password=“密码”,database=“数据库名”,charset=“utf8”)
# 得到一个可以执行SQL语句并且将结果作为字典返回的游标
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)
# 定义要执行的SQL语句
sql = """
CREATE TABLE USER1 (
id INT auto_increment PRIMARY KEY ,
name CHAR(10) NOT NULL UNIQUE,
age TINYINT NOT NULL
)ENGINE=innodb DEFAULT CHARSET=utf8;
"""
# 执行SQL语句
cursor.execute(sql)
# 关闭光标对象
cursor.close()
# 关闭数据库连接
conn.close()
```
<!-- more -->
## 插入数据
```python
# 导入pymysql模块
import pymysql
# 连接database
conn = pymysql.connect(host=“你的数据库地址”, user=“用户名”,password=“密码”,database=“数据库名”,charset=“utf8”)
# 得到一个可以执行SQL语句的光标对象
cursor = conn.cursor()
sql = "INSERT INTO USER1(name, age) VALUES (%s, %s);"
# 执行SQL语句,最好将语句变量放在execute中,放置sql注入
cursor.execute(sql, [name, age])
# 提交事务
conn.commit()
cursor.close()
conn.close()
```
### 批量操作
```python
# 导入pymysql模块
import pymysql
# 连接database
conn = pymysql.connect(host=“你的数据库地址”, user=“用户名”,password=“密码”,database=“数据库名”,charset=“utf8”)
# 得到一个可以执行SQL语句的光标对象
cursor = conn.cursor()
sql = "INSERT INTO USER1(name, age) VALUES (%s, %s);"
data = [("Alex", 18), ("Egon", 20), ("Yuan", 21)]
try:
    # 批量执行多条插入SQL语句
    cursor.executemany(sql, data)
    # 提交事务
    conn.commit()
except Exception as e:
    # 有异常，回滚事务
    conn.rollback()
cursor.close()
conn.close()
```
## 查询数据
```python
# 导入pymysql模块
import pymysql
# 连接database
conn = pymysql.connect(host=“你的数据库地址”, user=“用户名”,password=“密码”,database=“数据库名”,charset=“utf8”)
# 得到一个可以执行SQL语句的光标对象
cursor = conn.cursor()
# 查询数据的SQL语句
sql = "SELECT id,name,age from USER1 WHERE id=1;"
# 执行SQL语句
cursor.execute(sql)
# 获取单条查询数据
ret = cursor.fetchone()
cursor.close()
conn.close()
# 打印下查询结果
print(ret)
```
## 查询多条数据
``` python
# 导入pymysql模块
import pymysql
# 连接database
conn = pymysql.connect(host=“你的数据库地址”, user=“用户名”,password=“密码”,database=“数据库名”,charset=“utf8”)
# 得到一个可以执行SQL语句的光标对象
cursor = conn.cursor()
# 查询数据的SQL语句
sql = "SELECT id,name,age from USER1;"
# 执行SQL语句
cursor.execute(sql)
# 获取多条查询数据
ret = cursor.fetchall()
cursor.close()
conn.close()
# 打印下查询结果
print(ret)
```

## 进阶用法
```python
# 可以获取指定数量的数据
cursor.fetchmany(3)
# 光标按绝对位置移动1
cursor.scroll(1, mode="absolute")
# 光标按照相对位置(当前位置)移动1
cursor.scroll(1, mode="relative")
```

## 实际案例用法
使用pymysql封装好该功能
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @author  : bb
# @Version : 3.6

import pymysql
import sys
from utils import logs


class Usermysql(object):
    """
    封装控制mysql数据的增删改查功能
    """

    def __init__(self):
        self.addr = 'localhost'
        self.dbname = your_db
        self.username = your_username
        self.passwd = your_passwd
        self.charset = 'utf8'
        self.con = ''

    def db_init(self):
        # 尝试连接，成功就创建表，错误就报错终止程序
        try:
            con = pymysql.connect(
                host=self.addr, user=self.username, passwd=self.passwd, charset=self.charset)
        except:
            logs('数据库账号密码错误！')
            sys.exit()
        try:
            cur = con.cursor()
            cur.execute('create database %s character set utf8;' % self.dbname)
            con.commit()

        except Exception as e:
            logs('%s已存在%s数据库' % (e,self.dbname))
        cur.execute('use %s' % self.dbname)
        # 使用 execute() 方法执行 SQL，如果表存在则删除
        # cur.execute("DROP TABLE IF EXISTS EMPLOYEE")
        sql = """
        CREATE TABLE if not exists `infos` (
              `Id` int(11) NOT NULL AUTO_INCREMENT,
                `mobile` varchar(18) NOT NULL DEFAULT '',
              `bio` varchar(50) NOT NULL DEFAULT '',
              `username` varchar(50) NOT NULL DEFAULT '',
              `getime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
              `logintime` DATETIME,
              `note` varchar(20) DEFAULT '',
              PRIMARY KEY (`Id`)
              ) ENGINE=MyISAM CHARSET=utf8 ROW_FORMAT=DYNAMIC;
             """
        sql2 = """
        CREATE TABLE if not exists `groups` (
              `Id` int(11) NOT NULL AUTO_INCREMENT,
                `groupname` varchar(30) NOT NULL DEFAULT '',
              `username` varchar(50) NOT NULL DEFAULT '',
              `big` varchar(50) NOT NULL DEFAULT '',
              `nickname` varchar(50) NOT NULL DEFAULT '',
              `getime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
              `note` varchar(20) DEFAULT '',
              PRIMARY KEY (`Id`)
              ) ENGINE=MyISAM CHARSET=utf8 ROW_FORMAT=DYNAMIC;
              """
        sql3 = """
        CREATE TABLE if not exists `regs` (
              `Id` int(11) NOT NULL AUTO_INCREMENT,
              `mobile` varchar(18) NOT NULL DEFAULT '',
              `telstate` varchar(10) NOT NULL DEFAULT '',
              `messtate` varchar(10) NOT NULL DEFAULT '',
              `peoplestatu` varchar(10) NOT NULL DEFAULT '',
              `firstname` varchar(30) NOT NULL DEFAULT '',
              `secname` varchar(30) NOT NULL DEFAULT '',
              `filename` varchar(50) NOT NULL DEFAULT '',
              `logintime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
              `note` varchar(20) DEFAULT '',
              PRIMARY KEY (`Id`)
              ) ENGINE=MyISAM CHARSET=utf8 ROW_FORMAT=DYNAMIC;
                      """
        cur.execute(sql)
        cur.execute(sql2)
        cur.execute(sql3)
        con.close()
        logs('数据库初始化成功！')

    def db_login(self):
        # 查看是否可连接,且是否含正确的表
        try:
            con = pymysql.connect(host=self.addr, user=self.username,
                                  passwd=self.passwd, charset=self.charset, db=self.dbname)
        except:
            logs('数据库账号密码错误！')
            sys.exit()
        logs('数据库连接成功',types=2)
        self.con = con

    def db_add(self, tablename, colname, value):
        # 增 "INSERT INTO user(name,sex,age) VALUES ('%s', '%s', '%d')"
        try:
            cur = self.con.cursor()
            cur.execute('insert into %s(%s) value(\'%s\')' %
                        (tablename, colname, value))
            self.con.commit()
        except Exception as e:
            logs('%s插入数据错误'%e)

    def db_del(self, tablename, colname, value):
        # 删 "delete from user where id = '%d'"
        try:
            cur = self.con.cursor()
            cur.execute('delete from %s where %s = \'%s\'' %
                        (tablename, colname, value))
            self.con.commit()
        except Exception as e:
            logs('%s删除数据错误'%e)

    def db_update(self, tablename, colname, value, mobile):
        # 改 "update  user  set age = age + 1 ,sex = '%s' where id = '%d'"
        try:
            cur = self.con.cursor()
            cur.execute('update %s set %s = \'%s\' where mobile =\'%s\'' %
                        (tablename, colname, value, mobile))
            self.con.commit()
        except Exception as e:
            logs('%s修改数据错误'%e)

    def db_select(self, tablename, colname, value, types=2):
        # 查 "SELECT * FROM user  WHERE id = '%d'"
        try:
            cur = self.con.cursor()
            cur.execute('select * from %s where %s =\'%s\'' %
            (tablename, colname, value))
            if types == 2:
                ret = cur.fetchone()
            else:
                ret = cur.fetchall()                
            #print('查询结果为:%s'%json.dumps(ret))
            return ret
        except Exception as e:
            logs('查询数据错误%s'%e)

    def db_diysql(self, sql):
        # 自己传入数据库语句进行执行，适合批量脚本运行
        try:
            cur = self.con.cursor()
            cur.execute(sql)
            self.con.commit()
        except Exception as e:
            logs('命令执行错误')

```