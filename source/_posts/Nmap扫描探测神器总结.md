---
title: Nmap扫描探测神器总结
date: 2019-04-16 17:21:34
tags:
    - nmap 
    - 软件
    - 扫描
categories:
    - 笔记总结
    - 软件运用
---
### Nmap简介
Nmap 是一款网络扫描和主机侦测的非常有用的工具。合理使用，不仅可以用来信息收集和枚举，同时也可以用来作为一个漏洞探测器或安全扫描器。另外 Nmap 还跨平台，适用于 Windows、Linux 和 Macintosh 等主流操作系统，功能强大，乃居家旅行之必备良品。

Nmap 具有`主机发现`、`端口扫描`、`服务版本侦测`、`操作系统侦测`四大功能

这四大应用乃 Nmap 之精髓，同时它们之间具有逻辑上的顺序关系。首先进行主机判断，发现在指定信道上存活的主机，然后对这些存活的主机进行端口扫描，接着记录这些运行服务的版本号，最后针对不同操作系统我们还可以有不同的具体应对方案，顺手便把对方的操作系统给侦测了。

在这四大基本应用之上，Nmap 还有一些用来规避、绕过防火墙的组合技巧。
最后本文将介绍 Nmap 的一个强大的应用模块，名曰 Nmap 脚本引擎（Nmap Script Engine），简称 NSE。使用 NSE 来干一些本来看起来繁琐的事情，也就是说实现了自动化。

### 主机发现
```bash
nmap -sn IP地址 # 表示 “使用 ping 扫描来侦测存活的主机，而不进行端口扫描”
nmap -Pn IP地址 # 跳过主机发现
```
<!--more-->

### 端口扫描
```bash
nmap -sU IP地址 # UDP扫描。NTP（123端口）、SNMP（161端口）等服务，就必须使用 UDP 协议进行扫描

nmap -sV IP地址 # 扫描和版本号侦测
```
### 服务版本侦测
```bash
nmap -sV IP地址 # 扫描和版本号侦测
```

### 操作系统侦测
```bash
nmap -A IP地址 
nmap -O IP地址 
操作系统侦测有两个参数选项，其一是参数 O，其二是参数A，后者乃前者的冗余版本。我更多的使用 A 参数，以得到更多的信息
```
### 技巧
#### 利用掩体(参入假ip)
```bash
namp -D IP地址1,IP地址2... IP地址
```
你可以使用 D 选项（英文 decoy）跟一些 IP 地址，IP 和 IP 之间用逗号隔开。这样看起来用来侦测而发送的数据包不仅来自于你的 IP 地址，还来自于这些掩体 IP。这就叫做 “混入其中”。

#### 空闲扫描
```bash
nmap -sI 僵尸IP地址[:开放的僵尸端口] IP地址
```
和 D 选线不同的是，sI 根本不使用你自己的 IP 地址，而是使用空闲的网络资源。这样隐蔽性就更强了。开放的僵尸端口为选填，默认等于 80 端口。具体原理请参考 [Nmap](https://nmap.org/book/idlescan.html) 文档。根据这个理论，你不能使用空闲扫描来扫描你自己的主机 IP。在 msfconsole 中，你可以使用 auxiliary/scanner/ip/ipidseq 来完成这个工作。

#### 指定网卡进行扫描
```bash
nmap -e 网卡 IP地址
```
当你拥有不止一个网卡的时候，这很有用

#### 限制扫描时间
```bash
nmap --host-timeout 时间 IP地址
```
限制每个 IP 地址的扫描时间（单位为秒），当要扫描大量的主机 IP 时这很有用

#### 指定源 IP 地址
```bash
nmap -S 源IP地址 IP地址
```
使用冒充的 IP 地址进行扫描以增强隐蔽性。这里伪装成的 IP 也可以来自于下线状态的主机地址

#### 指定源主机端口
```bash
nmap -g 53 IP地址
```
使用 g 参数，或者 source-port 参数，来手动设定用来扫描的端口。常用的，如 20、53、67 端口

#### 添加垃圾数据
```bash
nmap --data-length 垃圾数据长度 IP地址
```
一些常见的扫描之数据包是有特定的数据长度的，通过在发送的数据包末尾添加随机的垃圾数据，以达到混淆视听的作效果

#### 随机选择扫描对象
```bash
nmap --randomize-hosts IP地址
```
如果你要扫描大量的，比如成百上千的主机 IP，这很有效。它会打乱扫描顺序，以规避检测系统的检测

#### 伪装 MAC 地址
```bash
nmap --spoof-mac 伪造MAC IP地址
```
你可以通过指定供应商的名字来伪装 MAC 地址。可选的名字有 Dell、Apple、3Com。当然也可以手动指定 MAC 地址的值。或者为了简单起见，可以在上面 “伪造IP” 的地方填写数字 0，这将生成一个随机的 MAC 地址
#### 伪造检验值
```bash
nmap --badsum IP地址
```
这将使用伪造的 TCP / UDP / SCTP 校验和发送数据

#### 扫描速度
```bash
nmap -T0 IP地址
```
T后面跟的数字代表扫描速度，数字越大则速度越快。0～5分别表示：妄想症、鬼鬼祟祟、彬彬有礼、正常、好斗、精神病

#### 跟踪路由
```bash
 nmap --traceroute <ip地址>
```
跟踪路由用于检测您的计算机数据包从路由器到ISP的路由到互联网直至其特定目的地

### Nmap脚本引擎
Nmap 脚本引擎内置在 Nmap 中，使用 script 参数进行调用。它的英文名是 Nmap Script Engine，简称 NSE。
Nmap 内置了一些已经写好的脚本，在 Kali 等主流渗透系统中被保存在 /usr/share/nmap/scripts/ 文件夹下。文件后缀名是 .nse。使用 sC（等价于 script=default）或者 script 参数对 Nmap 脚本进行调用

按类别扫描如下：
```bash
nmap --script=类别 IP地址
```
Nmap 的脚本类别分为以下几类:
- auth：负责处理鉴权证书、绕开鉴权的脚本。

- broadcast：处理在局域网内探查更多服务开启的状况，如 dhcp / dns / sqlserver 等服务。

- brute：提供暴力破解方式，针对常见的应用如 http / snmp 等。

- default：使用 sC 或 A 选项时默认的脚本，提供基本脚本扫描能力。

- discovery：挖掘更多的网络服务信息，如 smb 枚举、snmp 查询等。

- dos：用于进行拒绝服务攻击。

- exploit：利用已知的漏洞入侵系统。

- external：利用第三方的数据库或资源，如进行 whois 解析。

- fuzzer：模糊测试脚本，发送异常的包到目标主机，探测出潜在的漏洞。

- malware：探测目标是否感染了病毒，是否开启了后门。

- safe：与 fuzzer 功能相反，属于安全性脚本。

- version：负责增强信性服务与版本扫描功能的脚本。

- vuln：负责检查目标主机是否有常见的漏洞，如 ms08_067

#### 使用特定的脚本扫描
```bash
nmap --script=特定的脚本名字 IP地址
```
所谓 Nmap 脚本的名字，就是 /usr/share/nmap/scripts/ 文件夹下的那些文件，去掉后缀后的东西
    
#### 增强和个性化定制脚本
CVE（Common Vulnerablities and Exposures）是安全界的一个规范。一个较著名的，Exploit-DB 数据库以 CVE 规范为基础，对确定披露的漏洞进行了分类。我们将使用下面的脚本直接访问 Exploit-DB 数据库(并导入 Nmap。这是 [Exploit-DB 数据库的在线搜索接口](https://www.exploit-db.com/search?q=)。当然漏洞库多了去了，但我之所以将它列出，因为该地址默认是存储在 Kali 的收藏栏里面的
这里提供两个别人已经写好的脚本。（可以自己在 Github 寻找）

- https://github.com/vulnersCom/nmap-vulners

- https://github.com/scipag/vulscan

我已经安装好了，以下是一个使用示范。相比于普通的漏洞扫描，通常它会更多有价值的信息
```bash
nmap -sV --script 脚本之间用逗号隔开 --script-args 参数之间用逗号隔开 IP地址
```
除此之外你还可以编写自己的 Nmap 脚本，这里是一个较为基础的入门指南

### 参考资料
[Nmap入门指南](https://nmap.org/man/zh/man-host-discovery.html)
[Nmap使用技巧](https://blog.csdn.net/abc_12366/article/details/82807108)



