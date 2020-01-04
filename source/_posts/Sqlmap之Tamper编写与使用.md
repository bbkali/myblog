---
title: Sqlmap之Tamper编写与使用
date: 2019-04-09 13:13:28
tags:
    - Sqlmap
    - Tamper
    - SQL注入 
categories:
    - 学习资料
---

## Tamper 简介
sqlmap是一个自动化的SQL注入工具，而tamper则是对其进行扩展的一系列脚本，主要功能是对本来的payload进行特定的更改以绕过waf

## 最小的例子
如下为一个最小的Tamper例子，作用是将payload中的单、双引号替换为\\
```python
# sqlmap/tamper/escapequotes.py

from lib.core.enums import PRIORITY

__priority__ = PRIORITY.LOWEST

def dependencies():
    pass

def tamper(payload, **kwargs):
    return payload.replace("'", "\\'").replace('"', '\\"')
```
<!-- more -->

不难发现编写一个Tamper需要如下要求：
1. 脚本结构为priority变量定义和dependencies、tamper函数定义
2. priority定义脚本的优先级，用于有多个tamper脚本的情况
3. dependencies函数声明该脚本适用/不适用的范围，可以为空
4. tamper是主要的函数，接受的参数为payload和**kwargs
返回值为替换后的payload

## 详细介绍
### tamper 函数

__tamper是整个脚本的主体。主要用于修改原本的payload，存到sqlmap/tamper/下，执行的时候带上--tamper参数，就可以使用该tamper来绕过该过滤规则__

### dependencies函数
dependencies函数，就tamper脚本支持/不支持使用的环境进行声明，一个简单的例子如下：
```python
# sqlmap/tamper/echarunicodeencode.py

from lib.core.common import singleTimeWarnMessage

def dependencies():
    singleTimeWarnMessage("tamper script '%s' is only meant to be run against ASP or ASP.NET web applications" % os.path.basename(__file__).split(".")[0])

# singleTimeWarnMessage() 用于在控制台中打印出警告信息
```

### kwargs
在官方提供的47个tamper脚本中，kwargs参数只被使用了两次，两次都只是更改了http-header，这里以其中一个为例进行简单说明
```python
# sqlmap/tamper/vanrish.py

def tamper(payload, **kwargs):
    headers = kwargs.get("headers", {})
    headers["X-originating-IP"] = "127.0.0.1"
    return payload
```
这个脚本是为了更改X-originating-IP，以绕过WAF，另一个kwargs的使用出现于xforwardedfor.py，也是为了改header以绕过waf

### 部分常数值
```python
# sqlmap/lib/enums.py

class PRIORITY:
    LOWEST = -100
    LOWER = -50
    LOW = -10
    NORMAL = 0
    HIGH = 10
    HIGHER = 50
    HIGHEST = 100


class DBMS:
    ACCESS = "Microsoft Access"
    DB2 = "IBM DB2"
    FIREBIRD = "Firebird"
    MAXDB = "SAP MaxDB"
    MSSQL = "Microsoft SQL Server"
    MYSQL = "MySQL"
    ORACLE = "Oracle"
    PGSQL = "PostgreSQL"
    SQLITE = "SQLite"
    SYBASE = "Sybase"
    HSQLDB = "HSQLDB"
```

### 部分tamper作用
|Tamper文件名|作用|
|:----:|:----:|
|apostrophemask.py|用utf8代替引号|
|equaltolike.py|like 代替等号|
|space2dash.py|绕过过滤‘=’ 替换空格字符（”），（’ – ‘）后跟一个破折号注释，一个随机字符串和一个新行（’ n’）|
|greatest.py|绕过过滤’>’ ,用GREATEST替换大于号。|
|space2hash.py|空格替换为#号 随机字符串 以及换行符|
|apostrophenullencode.py|绕过过滤双引号，替换字符和双引号。|
|halfversionedmorekeywords.py|当数据库为mysql时绕过防火墙，每个关键字之前添加mysql版本评论|
|space2morehash.py|空格替换为 #号 以及更多随机字符串 换行符|
|appendnullbyte.py|在有效负荷结束位置加载零字节字符编码|
|ifnull2ifisnull.py|绕过对 IFNULL 过滤。|
|space2mssqlblank.py(mssql)|空格替换为其它空符号|
|base64encode.py|用base64编码替换|
|space2mssqlhash.py|替换空格|
|modsecurityversioned.py|过滤空格，包含完整的查询版本注释|
|space2mysqlblank.py|空格替换其它空白符号(mysql)|
|between.py|用between替换大于号（>）|
|modsecurityzeroversioned.py|包含了完整的查询与零版本注释|
|space2mysqldash.py|替换空格字符（”）（’ – ‘）后跟一个破折号注释一个新行（’ n’）|
|multiplespaces.py|围绕SQL关键字添加多个空格|
|space2plus.py|用+替换空格|
|bluecoat.py|代替空格字符后与一个有效的随机空白字符的SQL语句。|
|nonrecursivereplacement.py|双重查询语句。取代predefined SQL关键字with表示 suitable for替代（例如  .replace（“SELECT”、””)） filters|
|space2randomblank.py|代替空格字符（“”）从一个随机的空白字符可选字符的有效集|
|percentage.py|asp允许每个字符前面添加一个%号|
|sp_password.py|追加sp_password’从DBMS日志的自动模糊处理的有效载荷的末尾|
|chardoubleencode.py| 双url编码(不处理以编码的)|
|unionalltounion.py|替换UNION ALL SELECT UNION SELECT|
|charencode.py|url编码|
|randomcase.py|随机大小写|
|unmagicquotes.py|宽字符绕过 GPC  addslashes|
|randomcomments.py|用/**/分割sql关键字|
|versionedkeywords.py|Encloses each non-function keyword with versioned MySQL comment|
|charunicodeencode.py|字符串 unicode 编码|
|securesphere.py|追加特制的字符串|
|versionedmorekeywords.py|注释绕过|
|space2comment.py|Replaces space character (‘ ‘) with comments ‘/**/’|
|halfversionedmorekeywords.py|关键字前加注释|
