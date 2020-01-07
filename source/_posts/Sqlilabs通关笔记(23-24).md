---
 title: Sqlilabs通关笔记(23-24)
 date: 2020-01-07 16:10:35
 tags: SQL注入
 categories: SQL注入
---

## 第二十三关 基于GET错误的过滤注释

### 注入点判断
1. 老办法单引号反斜杠试了下确实报错
2. 但注释过不了怀疑注释有过滤
3. 查看源代码,发现将#和--替换成空了
```php
$reg = "/#/";
$reg1 = "/--/";
$replace = "";
$id = preg_replace($reg, $replace, $id);
$id = preg_replace($reg1, $replace, $id);
$fp=fopen('result.txt','a');
fwrite($fp,'ID:'.$id."\n");
fclose($fp);
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```
4. 直接上payload
> http://127.0.0.1/sqlilabs2/Less-23/?id=-1' union select 1,(select group_concat(username,password ) from users),3  and  '1' = '1

![2020-1-7-16-19-38](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-16-19-38)

<!--more-->

## 第二十四关 POST二次排序注入-存储型注入

### 注入点判断
1. 感觉界面大变样

![2020-1-7-16-23-42](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-16-23-42)

2. 登录进去又是改密码，猜测又是update语句

![2020-1-7-16-24-13](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-7-16-24-13)

3. 修改密码加反斜杠重新登录未见报错，查看源代码一探究竟
    - 有新用户注册文件 New_user.php
    - 修改密码文件 pass_change.php
    - 登录文件 login.php
    - 都使用了mysql_real_escape_string函数对注册的参数进行过滤
    - 但在修改密码文件中却是直接调用username参数
    - 猜测可以之间注册一个admin'#用户进而修改admin的密码，代码如下

```php
if (isset($_POST['submit']))
{
	
	
	# Validating the user input........
	$username= $_SESSION["username"];
	$curr_pass= mysql_real_escape_string($_POST['current_password']);
	$pass= mysql_real_escape_string($_POST['password']);
	$re_pass= mysql_real_escape_string($_POST['re_password']);
	
	if($pass==$re_pass)
	{	
		$sql = "UPDATE users SET PASSWORD='$pass' where username='$username' and password='$curr_pass' ";
		$res = mysql_query($sql) or die('You tried to be smart, Try harder!!!! :( ');
		$row = mysql_affected_rows();
		echo '<font size="3" color="#FFFF00">';
		echo '<center>';
		if($row==1)
		{
			echo "Password successfully updated";
	
		}
		else
		{
			header('Location: failed.php');
			//echo 'You tried to be smart, Try harder!!!! :( ';
		}
	}
	else
	{
		echo '<font size="5" color="#FFFF00"><center>';
		echo "Make sure New Password and Retype Password fields have same value";
		header('refresh:2, url=index.php');
	}
}
```

因为没有报错注入的条件，时间盲注有点漫长可以尝试脚本注册然后再注入，确实有点麻烦

**但本题目的是：对于存储型的注入,可以先将导致SQL注入的字符预先存到数据库中,当再次调用到这个恶意构造的字符时就可以触发注入**

