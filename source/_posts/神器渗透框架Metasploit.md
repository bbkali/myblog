---
title: 神器渗透框架Metasploit
date: 2019-04-14 22:32:19
tags:
    - msf
    - 软件
categories:
    - 笔记总结
    - 软件运用
---
Metasploit是目前世界上领先的渗透测试工具，也是信息安全与渗透测试领域最大的开源项目之一。它彻底改变了我们执行安全测试的方式。

Metasploit之所以流行，是因为它可以执行广泛的安全测试任务，从而简化渗透测试的工作。Metasploit适用于所有流行的操作系统，本书中，主要以Kali Linux为主。因为Kali Linux预装了 Metasploit 框架和运行在框架上的其他第三方工具
Metasploit框架具有模块化的体系结构，exploits、payload、encoders都是独立的模块
![体系结构](https://image.3001.net/images/20190415/1555306186_5cb416ca0d783.png)

### 快速上手
1. 第一步：search name模块
2. 第二步：use name模块
3. 第三步：info 查看模块信息
4. 第四步：show payloads 查看该模块可以使用的攻击载荷（为scanner的时候不需要）
5. 第五步：set payload 载荷
6. 第六步：show targets查看该攻击载荷使用的系统类型（为scanner的时候不需要）
7. 第七步：set targets num 设置目标的系统类型
8. 第八步：show options 查看需要设置的参数
<!--more-->

### 备注总结
1. 利用host 和 services命令可以查看当前数据库的存储信息
2. msfconsole -r 1.sh 直接运行脚本命令
3. 进入msf后使用makerc记录键盘命令
4. 进入msf后使用sources导入运行脚本命令

### 参考文献
[快速入门](https://www.freebuf.com/column/200973.html)
[快速入门2](https://www.freebuf.com/column/201331.html)