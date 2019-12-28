---
title: kali更新技巧
date: 2019-04-11 13:25:35
tags:
    - kali
    - 更新
categories:
    - 学习资料
    - kali
---
### 更新出错问题解决
在kali下更新老是出现错误，如下:
```bash
W: GPG error: http://mirrors.ustc.edu.cn/kali kali-rolling InRelease: 
The following signatures were invalid: EXPKEYSIG ED444FF07D8D0BF6 
Kali Linux Repository <devel@kali.org> 
E: The repository 
'http://mirrors.ustc.edu.cn/kali kali-rolling InRelease' is not signed. 
N: Updating from such a repository can't be done securely, 
and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and 
user configuration details.

```
__是因为系统太久没有更新了__
解决的办法如下：
<!--more-->
1. 升级apt的key
```bash
wget -q -O - https://archive.kali.org/archive-key.asc | apt-key add
```
2. 更新仓库资源，配置文件在/etc/apt/source.list
```bash
#中科大 
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib 
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib 
#阿里云 
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib 
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
#清华大学 
deb http://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free 
deb-src https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free 
#浙大 
deb http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free 
deb-src http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free
```
3. 进行更新
```bash
apt-get update 更新资源索引文件
apt-get upgrade 更新软件包
apt-get dist-upgrade 更新系统

可以直接一条命令更新
apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y
```

### 文件目录映射对应表
通过更改下面的指定文件目录位置进行映射设置
```bash
root@kali:~# cat ~/.config/user-dirs.dirs
# This file is written by xdg-user-dirs-update
# If you want to change or add directories, just edit the line you're
# interested in. All local changes will be retained on the next run
# Format is XDG_xxx_DIR="$HOME/yyy", where yyy is a shell-escaped
# homedir-relative path, or XDG_xxx_DIR="/yyy", where /yyy is an
# absolute path. No other format is supported.
# 
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Downloads"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"
```