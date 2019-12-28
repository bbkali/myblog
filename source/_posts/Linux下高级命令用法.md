---
title: Linux下高级命令用法
date: 2019-04-04 09:44:18
tags:
    - Linux
categories:
    - 笔记总结
---

## grep

grep （缩写来自Globally search a Regular Expression and Print）是一种强大的文本搜索工具，它能使用特定模式匹配（包括正则表达式）搜索文本，并默认输出匹配行,grep和sed的区别在于，grep是以行为单位，进行字符串的对比，sed则可以进行删除、替换等更多的功能

> 语法：grep [-n] [-A] [-B] [--color==auto] '搜索的字符串' filename

```bash
-c（count） ：打印符合要求的行数

-i （ignore-case）：忽略大小写

-n （line-number）：在输出符合要求的行的同时连同行号一起输出

-v （invert-natch）：打印不符合要求的行,反向选择

-A （after-context）：后跟一个数字（有无空格都可以），例如 –A2则表示打印符合要求的行以及下面两行

-B （before-context）：后跟一个数字，例如 –B2 则表示打印符合要求的行以及上面两行

-C （context）：后跟一个数字，例如 –C2 则表示打印符合要求的行以及上下各两行

-E  与egrep执行模式相同,才能使用扩展的正则表达式

```
<!-- more -->

### 案例
```bash
root@kali:~# cat test.txt 

123
abc
456

abc123

#adff

23####
\n##
##
#adddff
```
1. 查看非空行和非注释开头的行并输出行号
    ```bash
    root@kali:~# grep -Evn '^$|^#' test.txt
    2:123
    3:abc
    4:456
    6:abc123
    10:23####
    11:\n##
    ```
2. 过滤出带有某个关键词的行并输入行号
    ```bash
    root@kali:~# grep -vn 'df' test.txt    
    1:
    2:123
    3:abc
    4:456
    5:
    6:abc123
    7:
    9:
    10:23####
    11:\n##
    12:##
    ```
3. 指定要过滤字符出现的次数
    ```bash
    root@kali:~# grep -En 'f{2}' test.txt 
    8:#adff
    13:#adddff
    ```
4. 清理进入的日志
```bash
grep /home/member/logs/access.log -v 174.122.136.104 -output cleaned.log
```

## sed
sed全称是：`Stream EDitor`（流编辑器）,本身是一个管道命令,并不对真实文件内容修改。
SED是一项Linux指令，功能同awk类似，差别在于，sed简单，对列处理的功能要差一些，awk的功能复杂，对列处理的功能比较强大。
grep工具的功能其实还不够强大，grep实现的只是查找功能，而它却不能实现把查找的内容替换掉。以前用vim的时候，可以查找也可以替换，但是只局限于在文本内部来操作，而不能输出到屏幕上。sed工具以及下面要讲的awk工具就能实现把替换的文本`输出到屏幕上`的功能了，而且还有其他更丰富的功能。sed和awk都是流式编辑器，是针对文档的行来操作的

> 语法： sed 参数 文件

```bash
-e command,–expression=command 直接在指令列模式上进行 sed 的动作编辑；。

-h,–help打印帮助，并显示bug列表的地址。

-n,–quiet,–silent取消默认输出,使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN的资料一般都会被列出到萤幕上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来

-f,–file=script-file <script文件> 以选项中指定的script文件来处理输入的文本文件。

-V,–version打印版本和版权信息

-i 直接修改读取的档案内容，而不是由萤幕输出

动作说明：

    a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
    c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
    d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
    i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
    p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
    s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！
        注意:‘s’ 就是替换的命令， ‘g’ 为本行中全局替换，如果不加 ‘g’ 只换该行中出现的第一个。除了可以使用 ‘/’ 作为分隔符外，还可以使用其他特殊字符例如 ‘#’ 或者 ‘@’ 都没有问题

```
### 案例
1. 显示
    ```bash
    sed -n '1,2p' test.txt ##显示一二两行，逗号间隔^表示开通$表示结尾
    ```
2. 新增(目前的下一行)
    ```bash
    sed '1,2a sdg' test.txt ## 在一二两行后添加一行sgd
    ```
3. 删除
    ```bash
    sed -n '1,2d' test.txt ##显示一二两行，逗号间隔^表示开通$表示结尾
    ```
4. 取代
    ```bash
    sed -n '1,20s/old/new/g' test.txt
    sed '1,2c hi' test.txt ## 将第一二两行替换为hi 
    ```
5. 插入(目前的上一行)
    ```bash
    sed -i '$a bye' test.txt ##在文件ab中最后一行直接输入"bye"
    ```
6. 查询
    ```bash
    sed -n '/关键字/p' test.txt
    ```
7. 删除匹配行
    ```bash
    sed -i '/匹配字符串/d'  filename  （注：若匹配字符串是变量，则需要“”，而不是‘’。记得好像是）
    ```
8. 替换匹配行中的某个字符串
    ```bash
    sed -i '/匹配字符串/s/替换源字符串/替换目标字符串/g' filename
    ```

## awk
AWK是一种处理文本文件的语言，是一个强大的文本分析工具。
之所以叫AWK是因为其取了三位创始人 Alfred Aho，Peter Weinberger, 和 Brian Kernighan 的Family Name的首字符

> 语法： awk [选项参数] 'script' var=value file(s) 或awk [选项参数] -f scriptfile var=value file(s)
> 

### 案例
1. 截取文档中的某个段
```bash
awk -F ':' '{print$2}' test.txt
```
解释一下:
- -F 选项的作用是指定分隔符，如果不加-F指定，则以空格或者tab为分隔符。 Print为打印的动作，用来打印出某个字段。$1为第一个字段，$2为第二个字段，依次类推，有一个特殊的那就是$0，它表示整行。
- 注意awk的格式，-F后紧跟单引号，然后里面为分隔符，print的动作要用 { } 括起来，否则会报错。print还可以打印自定义的内容，但是自定义的内容要用双引号括起来


2. 匹配
```bash
# 以冒号分隔，让第一个字符段中匹配有123
awk -F ':' '$1 ~/123/' test.txt

# 多次匹配
awk -F ':' '/root/ {print $1,$3} /test/ {print $1,$3}' /etc/passwd
```
3. 条件操作符
```bash
 awk -F ':' '$3=="0"' /etc/passwd

 awk中是可以用逻辑符号判断的，比如 ‘==’ 就是等于，也可以理解为 ‘精确匹配’ 另外也有 >, ‘>=, ‘<, ‘<=, ‘!= 等等，值得注意的是，即使$3为数字，awk也不会把它当数字看待，它会认为是一个字符。所以不要妄图去拿$3当数字去和数字做比较。

    != 为不匹配
另外还可以使用 && 和 || 表示 “并且” 和 “或者” 的意思。

```
4. 内置变量
```bash
awk常用的变量有：
NF ：用分隔符分隔后一共有多少段
NR ：行数

[root@localhost ~]# head -n3 /etc/passwd 
root:x:0:0:usr:/root:/bin/bash 
bin:x:1:1:bin:/bin:/sbin/nologin 
daemon:x:2:2:daemon:/sbin:/sbin/nologin 
[root@localhost ~]# head -n3 /etc/passwd  | awk -F ':' '{print NF}' 
7 7 7 
[root@localhost ~]# head -n3 /etc/passwd  | awk -F ':' '{print $NF}' 
/bin/bash /sbin/nologin /sbin/nologin 
[root@localhost ~]# head -n3 /etc/passwd  | awk -F ':' '{print NR}' 
1 2 3

```
5. 使用判断条件
```bash
[root@localhost ~]# awk 'NR>40' /etc/passwd 
gnome-initial-setup:x:989:983::/run/gnome-initial-setup/:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin 
avahi:x:70:70:Avahi mDNS/DNS-SD 
Stack:/var/run/avahi-daemon:/sbin/nologin 
cao:x:1001:1001::/home/cao:/bin/bash
usr:x:1002:1002:用户,offic,1234567,123:/home/usr:/bin/bash
test:x:1003:1003::/home/test:/bin/bash 
test1:x:1004:1003::/home/test1:/bin/bash

```

