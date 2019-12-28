---
title: 专治wordpress的wpscan
date: 2019-04-12 06:08:45
tags:
    - Wpscan
    - Wordpress
    - 软件
categories:
    - 笔记总结
    - 软件运用
---

### 简介
WPScan是一个扫描WordPress漏洞的黑盒子扫描器，可以扫描出wordpress的版本，主题，插件，后台用户以及爆破后台用户密码等

### 命令介绍
```bash
--enumerate | -e 接后面参数
    u 枚举用户
    p plugins插件程序
    vp 仅漏洞插件程序
    ap 所有插件程序
    t  themes主题
    at 仅漏洞主题
```
<!--more-->
### 常用案例

1. 通过漏洞插件扫描用户
```bash
wpscan -u 127.0.0.1/wordpress -e u vp
命令详解 -e使用枚举方式  u 扫描ID1-ID10   vp扫描漏洞插件
```
2. 使用密码字典对用户进行爆破
```bash
wpscan -u 127.0.0.1/wordpress -e u --wordlist /root/wordlist.txt -t 50 

命令详解： -e枚举方式 u 用户ID1-ID10  -t 指定线程数 --wordlist使用指定字典进行密码爆破 /root/wordlist.txt 字典路径及字典文件  wordlist.txt字典文件需自己准备或使用kali自带字典
```
3. 可结合扫描出来的用户进行爆破
4. 可结合扫描出来的漏洞插件+msf进行漏洞利用

### 参考文章
[Wpscan完整攻略](https://www.cnblogs.com/WangYiqiang/p/9490869.html)
[Wpscan扫描](https://www.cnblogs.com/WangYiqiang/p/9490869.html)