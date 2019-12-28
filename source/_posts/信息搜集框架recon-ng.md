---
title: 信息搜集框架recon-ng
date: 2019-04-12 06:09:08
tags:
    - 信息搜集
    - Recon-ng
categories:
    - 笔记总结
    - 软件运用
---
在web渗透测试中，信息侦察与收集相当重要，每个经验丰富的黑客都有自己独特的信息收集妙招，信息搜集是渗透的第一步，也是至关重要的一步，信息的多少决定攻击的范围有多少。
信息收集分为三步：`侦察Reconnaissance`、`发现Discovery`与`汇报reporting`。这也对应了该工具的三个模块，现在就记录一下Recon-ng 这款较新的信息侦察收集工具的使用

## 快速上手
以web应用指纹识别为例子
### 第一步：搜索指定模块
```bash
recon-ng > search whatweb
[*] Searching for 'whatweb'

Recon:
-------------------------------
recon/hosts/enum/http/whatweb
```
<!--more-->
### 第二步：使用该模块
```bash
recon-ng > use recon/hosts/enum/http/whatweb 
```
### 第三步：查看模块信息 
```bash
recon-ng [whatweb] > info

  Name:
    WhatWeb Web Technologies scan

  Author:
    thrapt (thrapt@gmail.com) and Tim Tomes (@LaNMaSteR53)

  Description:
    Leverages WhatWeb.net to recognise web technologies being used.

  Options:

    Name     Current Value  Req  Description
    -------  -------------  ---  -----------
    SOURCE   db             yes  source of module input
    VERBOSE  True           yes  verbose output

  Comments:
    Source options: [ db | <hostname> | ./path/to/file | query <sql> ]
```
### 第四步：设置选参数
```bash
recon-ng [whatweb] > set SOURCE resources.infosecinstitute.com
SOURCE => resources.infosecinstitute.com
```

### 第五步：运行
```bash
recon-ng [whatweb] > run
```
### 模块枚举
Recon-ng约有80个recon模块，2个发现模块，2个exp利用模块，7个报告模块和2个导入模块
```bash
cache_snoop – DNS缓存录制

interesting_files – 敏感文件探测

command_injector – 远程命令注入shell接口

xpath_bruter – Xpath注入爆破

csv_file – 高级csv文件导入

list – List文件导入

point_usage – Jigsaw – 统计信息提取用法

purchase_contact – Jigsaw – 简单的联系查询

search_contacts – Jigsaw联系枚举

jigsaw_auth – Jigsaw认证联系枚举

linkedin_auth – LinkedIn认证联系枚举

github_miner – Github资源挖掘

whois_miner – Whois数据挖掘

bing_linkedin – Bing Linkedin信息采集

email_validator – SalesMaple邮箱验证

mailtester – MailTester邮箱验证

mangle – 联系分离

unmangle –联系反分离

hibp_breach –Breach搜索

hibp_paste – Paste搜索

pwnedlist – PwnedList验证

migrate_contacts – 域名数据迁移联系

facebook_directory – Facebook目录爬行

fullcontact – FullContact联系枚举

adobe – Adobe Hash破解

bozocrack – PyBozoCrack Hash 查询

hashes_org – Hashes.org Hash查询

leakdb – leakdb Hash查询

metacrawler – 元数据提取

pgp_search – PGP Key Owner查询

salesmaple – SalesMaple联系获取

whois_pocs – Whois POC获取

account_creds – PwnedList – 账户认证信息获取

api_usage – PwnedList – API使用信息

domain_creds – PwnedList – Pwned域名认证获取

domain_ispwned – PwnedList – Pwned域名统计获取

leak_lookup – PwnedList – 泄露信息查询

leaks_dump – PwnedList –泄露信息获取

brute_suffix – DNS公共后缀爆破

baidu_site – Baidu主机名枚举

bing_domain_api – Bing API主机名枚举

bing_domain_web – Bing主机名枚举

brute_hosts – DNS主机名爆破

builtwith – BuiltWith枚举

google_site_api – Google CSE主机名枚举

google_site_web – Google主机名枚举

netcraft – Netcraft主机名枚举

shodan_hostname – Shodan主机名枚举

ssl_san – SSL SAN查询

vpnhunter – VPNHunter查询

yahoo_domain – Yahoo主机名枚举

zone_transfer – DNS域文件收集

ghdb – Google Hacking数据库

punkspider – PunkSPIDER漏洞探测

xssed – XSSed域名查询

xssposed – XSSposed域名查询

migrate_hosts – 域名数据迁移host

bing_ip – Bing API旁站查询

freegeoip –FreeGeoIP ip定位查询

ip_neighbor – My-IP-Neighbors.com查询

ipinfodb – IPInfoDB GeoIP查询

resolve – 主机名解析器

reverse_resolve – 反解析

ssltools – SSLTools.com主机名查询

geocode – 地理位置编码

reverse_geocode – 反地理位置编码

flickr – Flickr地理位置查询

instagram – Instagram地理位置查询

picasa – Picasa地理位置查询

shodan – Shodan地理位置查询

twitter – Twitter地理位置查询

whois_orgs – Whois公司信息收集

reverse_resolve – 反解析

shodan_net – Shodan网络枚举

census_2012 – Internet Census 2012 查询

sonar_cio – Project Sonar查询

migrate_ports – 主机端口数据迁移

dev_diver – Dev Diver Repository检查

linkedin – Linkedin联系获取

linkedin_crawl – Linkedin信息抓取

namechk – NameChk.com用户名验证

profiler – OSINT HUMINT信息收集

twitter – Twitter操作

github_repos – Github代码枚举

gists_search – Github Gist搜索

github_dorks – Github Dork分析

csv – CSV文件生成

html – HTML报告生成

json – JSON报告生成

list – List生成

pushpin – PushPin报告生成

xlsx – XLSX文件创建

xml – XML报告生成
```
可以自定义编写模块,找到个现成的可自行下载使用
[Recon-ng-modules](https://github.com/scumsec/Recon-ng-modules)
### 参考文献
[官方文档](https://bitbucket.org/LaNMaSteR53/recon-ng)
[Recon-ng侦察收集工具](https://www.freebuf.com/articles/web/7385.html)
