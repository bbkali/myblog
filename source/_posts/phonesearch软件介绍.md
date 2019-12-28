---
title: phonesearch软件介绍
date: 2019-02-21 10:16:04
tags: 
  - 软件
  - search
  - 爬虫
categories:
  - 实践运用
  - 软件运用
---
## phonesearch简介

---
 __一个本地批量查询手机归属地及是否注册`Telegram`、`Viber`、`Whatsapp`、`Gmail`等app这是一个简单项目__

---

## phonesearch有哪些功能？

* 查询单个号码归属地及注册4种app情况
* 从文件中读取号码批量查询号码归属地及注册4种app情况
* 从文件中读取号码储存为vcf通讯录格式
* 查询结果将返回号码所属运营商、省份、城市、卡类型、注册app情况等字段
* 将查询结果保存为result.txt文件

## 用法
<!--more-->
### 获取项目
- 目前仅支持exe封装格式，仅支持联系获取安装包


### 运行程序
- 打开cmd将exe文件拖拉进去执行,可加--help查看帮助信息

```
phonesearch.exe --help
```

```
|  _  \ | | | | /  _  \ |  \ | | | ____| /  ___/ | ____|     /   | |  _  \  /  ___| | | | |
| |_| | | |_| | | | | | |   \| | | |__   | |___  | |__      / /| | | |_| |  | |     | |_| |
|  ___/ |  _  | | | | | | |\   | |  __|  \___  \ |  __|    / / | | |  _  /  | |     |  _  |
| |     | | | | | |_| | | | \  | | |___   ___| | | |___   / /  | | | | \ \  | |___  | | | |
|_|     |_| |_| \_____/ |_|  \_| |_____| /_____/ |_____| /_/   |_| |_|  \_\ \_____| |_| |_|

                                                    ---Designed by bb

usage: phonesearchx.exe [-h] [-r [TXT [TXT ...]]] [-n [NUMBER [NUMBER ...]]]
                        [-f [FILE [FILE ...]]] [-v VCF]

这是一个离线的号码归属地及是否涉外app号码查询软件

optional arguments:
  -h, --help            show this help message and exit
  -r [TXT [TXT ...]], --txt [TXT [TXT ...]]
                        要批量的手机号码列表文件
  -n [NUMBER [NUMBER ...]], --number [NUMBER [NUMBER ...]]
                        要查询的单个手机号码
  -f [FILE [FILE ...]], --file [FILE [FILE ...]]
                        可配合-r使用,要导出查询结果的文件名
  -v VCF, --vcf VCF     可配合-r使用,要保存为vcf格式的通讯录文件

example:phonesearch.exe -n [phonenumber]或者phonesearch.exe -r [numberfile.txt]
-f result.txt
```

### 查询单个号码

``` 
phonesearch.exe -n 130????????
```
### 从文件中读取号码批量查询

``` 
phonesearch.exe -r yourtext.txt -f result.txt
```
### 从文件中读取号码存储为vcf通讯录格式

``` 
phonesearch.exe -r yourtext.txt -v result.vcf
```

## 常见问题
1. 运行程序显示不兼容
exe程序提供32位和64位操作系统程序，请根据自身系统位数运行相应phonesearch.exe 程序

2. 运行程序显示无法加载动态库
系统未安装Visual C++，[32位点击下载](download.microsoft.com/download/9/3/F/93FCF1E7-E6A4-478B-96E7-D4B285925B00/vc_redist.x86.exe)，[64位点击下载](download.microsoft.com/download/9/3/F/93FCF1E7-E6A4-478B-96E7-D4B285925B00/vc_redist.x64.exe)

3. 安装Visual C++ 不成功
请参照查看[帖子内容](https://appuals.com/how-to-fix-the-procedure-entry-point-ucrtbase-terminate-is-missing-or-could-not-be-located/)自行解决

4. 其它未知错误
对不起，你的电脑太老了，换台电脑或者重装最新系统吧！

## 关于反馈
在使用中有任何问题，欢迎反馈给我，同时需要增加新功能，可以用以下联系方式跟我交流
- 邮件(shuiyue75381#gmail.com, 把#换成@)




