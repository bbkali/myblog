---
title: 生成字典神器Crunch
date: 2019-03-07 13:36:32
tags:
    - kali
    - Crunch
    - wifi
    - 软件
categories:
    - 笔记总结
    - 软件运用
---
## 下载编译
[下载地址](https://sourceforge.net/projects/crunch-wordlist/),也可手动命令进行下载、解压和编译
```
wget https://sourceforge.net/projects/crunch-wordlist/files/crunch-wordlist/crunch-3.6.tgz 
tar -zxvf crunch-3.6.tgz 
cd crunch-3.6/ 
make 
```
## 命令格式
```bash
crunch <min> <max> [options]
```
<!--more-->
参数详解:
```bash
     min    设定最小字符串长度（必选）
     max    设定最大字符串长度（必选）
     
     oprions
     -b     指定文件输出的大小，避免字典文件过大(mib为单位)
     -c     指定文件输出的行数，即包含密码的个数
     -d     限制大于相同元素连续出现的次数(-d 2 时cac符合条件)
     -e     定义停止字符，即到该密码就停止生成
     -f     调用库文件元素列表（/etc/share/crunch/charset.lst）
     -i     改变输出格式，即aaa,aab -> aaa,baa
     -I     通常与-t联合使用，表明该字符为实义字符
     -m     通常与-p搭配
     -o     将密码保存到指定文件，-o START 为默认“首密码-尾密码”命名
     -p     指定元素以排列组合的方式进行,以空格分隔
     -q     读取密码文件，即读取pass.txt
     -r     定义重某一字符串重新开始
     -s     指定一个开始的字符，即从自己定义的密码xxxx开始
     -t     指定密码输出的格式(可以引用特殊字符)
     -u     禁止打印百分比（必须为最后一个选项）
     -z     压缩生成的字典文件，支持gzip,bzip2,lzma,7z,不加默认为txt
```


## 特殊字符
```bash
     %      代表数字
     ^      代表特殊符号
     @      代表小写字母
     ,      代表大写字符
```

## 实践案例
1. 生成最小5位，最大8位，由26个小写字母为元素的所有组合
```bash
crunch 5 8
```
2. 生成最小为5,最大为6，由字符串abcde开头，以字符串gggggg为结束的所有字符组合.以abcdef为元素生成.
```bash
crunch 5 6 abcdef
```
3. 生成最小为5,最大为6，由abcdefg和空格为元素的所有组合(/代表空格)
```bash
crunch 5 6 abcdefg\ 或者 crunch 5 6 "abcdefg " 
```
4. 调用密码库charset.lst，生成最小为5，最大为8,元素为密码库charset.lst中 mixalpha-numeric-all-space的项目，并保存为 wordlist.txt
```bash
crunch 5 8 -f charset.lst mixalpha-numeric-all-space -o wordlist.txt 

# 其中charset.lst在kali_linux的目录为 /usr/share/crunch/charset.lst
# charset.lst中mixalpha-numeric-all-space项目包含最常见的元素组合(即大小写字母+数字+常见符号)
# 使用cat /usr/share/crunch/charset.lst 查看所有密码库

# 常用的如下，以元素类型组合的名字命名
hex-lower                     = [0123456789abcdef]
hex-upper                     = [0123456789ABCDEF]
numeric                       = [0123456789]
numeric-space                 = [0123456789 ]
symbols14                     = [!@#$%^&*()-_+=]
ualpha                        = [ABCDEFGHIJKLMNOPQRSTUVWXYZ]
ualpha-space                  = [ABCDEFGHIJKLMNOPQRSTUVWXYZ ]
ualpha-numeric                = [ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789]
ualpha-numeric-space          = [ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 ]
ualpha-numeric-symbol14       = [ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!@#$%^&*()-_+=]
ualpha-numeric-symbol14-space = [ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!@#$%^&*()-_+= ]
ualpha-numeric-all            = [ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!@#$%^&*()-_+=~`[]{}|\\:;\"\'<>,.?/]
lalpha                        = [abcdefghijklmnopqrstuvwxyz]
lalpha-space                  = [abcdefghijklmnopqrstuvwxyz ]
```
5. 生成两位小写字母+dog+三位小写字母,并以cbdogaaa字符串开始字典生成.调用密码库charset.lst，生成8位密码;其中元素为密码库 charset.lst中mixalpha-numeric-all-space的项
```bash
crunch 8 8 -f /usr/share/crunch/charset.lst mixalpha-numeric-all-space -t @@dog@@@ -s cbdogaaa
```
6. 生成某个字符串的排列组合.
```bash
crunch 4 4 -p abc

# 生成单词的排列组合
crunch 4 5 -p dog cat bird 
```
7. 生成6000个密码且生成文件压缩为bzip2格式
```bash
crunch 1 5 -o START -c 6000 -z bzip2
```
8. 生成最小为4位，最大为5位元素为所有小写字母的密码字典，并以20M进行分割
```bash
# 类型有效值为KB、MB、GB、KIB，MIB，和GIB
# 前三种类型是基于1000，而最后三种类型是基于1024注意数字与类型之间没有空格
crunch 4 5 -b 20mib -o START
```
9. 生成3位密码，其中第一位由“a，b，c”中的一个;第二位为“1,2,3”中的一个;第三位为“!，@，#”中的一个。比如1a!、2a#、3b@.....
```bash
# 加号(+)是一个占位符，以便为字符类型指定一个字符集。crunch将使用默认字符集的字符类型，当crunch遇到一个+(加号)的命令行。必须为每个字符类型指定值或使用加号。也就是说，如果你有两个字符类型，你要么为每个类型指定值，要么使用加号。
crunch 3 3 abc + 123 \!@#  -t  ^%@ 
```
10. 生成7位密码，格式为“字符p@ss”+大写字母+数字+符号，比如p@ssZ9>......
```
#加-l选项是将字符串中的@作为文字字符集，而不是做为小写字母进行替换
crunch 7 7 -t p@ss,%^  -l a@aaaaa
```
11. 生成5位密码，格式为三个小写字母+两个数字，并限制每个密码最少出现2种字母，以aab00开头，以zzy99结束。“-d 2@”表示字母连续重复最多2次
```bash
crunch 5 5 -d 2@ -t @@@%% 
```
12. 调用密码库unicode_test.lst中的japanese项目字符，生成4位密码，其中格式为两小写字母+两数字
```
crunch 4 4 -f unicode_test.lst japanese -t @@%% -l @xdd 
```

## 常用的命令
1. 生成六位小写字母密码，其中前四位为pass
```bash
crunch 6 6 -t pass@@ -o mm.txt
```
2. 制作8位纯数字字典
```
crunch 8 8 charset.lst numeric -o num8.txt
```
3. 制作139开头的手机密码字典
```
crunch 11 11  +0123456789 -t 139%%%%%%%% -o num13.dic 
```
4. 在线使用生成的密码,参数最后面的-表示引用crunch生成的密码，例如无线密码在线破解
```
crunch 2 4 0123456789 | aircrack -ng a,cap -e MyESSID -w - 
crunch 10 1012345 --stodout | airolib -ng testdlb -import passwd –  
crunch 1 6 0123456789 | john pwd.txt --stdin - 
```

## 参考拓展
1. [在线生成社工字典](https://www.bugku.com/mima/)
2. 支持中文的交互生成字典工具`cupp`,[github地址](https://github.com/Saferman/cupper.git)
3. 其他好用的生成字典的工具cewl,自行百度
4. [参考资料](http://netsecurity.51cto.com/art/201706/541638.htm)