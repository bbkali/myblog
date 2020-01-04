---
title: ESP8226制作WIFI钓鱼
date: 2019-05-14 21:09:01
tags:
    - wifi 
    - esp8226
categories:
    - 实践运用
    - 软件
---
## ESP8826简介

## 制作WIFI Deauther
[esp8266_deauther项目地址](https://github.com/spacehuhn/esp8266_deauther/wiki/Installation)
可以通过网页或者手动选择，发现对wifi的攻击、生成热点等功能

### 原理
是通过发送取消认证报文让客户端以为路由器需要让自己断开连接从而断开wifi，伪造路由器向客户端发送取消认证报文，使客户端主动断开wifi连接。并且支持自定义热点伪造（批量生成假Wi-Fi信号）
### 缺点
1. 只不过散热非常差，长时间运行担忧啊
2. 由于esp8266只支持802.11bgn,所以以下实验只针对2.4GWiFi测试。不支持5GWi-Fi！

<!--more-->
## 步骤
### 1.连接ESP8826
先电脑安装对应芯片驱动使用USB线串联设备
对应芯片驱动下载地址：
[CP2102](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)
[CH340](https://sparks.gogo.co.nz/ch340.html)
![493a3eadcbe043e93fbf162fc2ce986d.png](en-resource://database/5913:1)
一直点击下一步就可以完成安装了安装完之后，就可以将板子连到电脑上了，这时候在设备管理器里会出现板子的型号，以及端口号。![c20c236daaceb9ee45ae638521bb60a9.png](en-resource://database/5915:1)
红框标出来的就是板子的型号以及端口

### 2.下载刷入固件软件和固件
[flasher下载地址](https://github.com/nodemcu/nodemcu-flasher/raw/master/Win32/Release/ESP8266Flasher.exe)
[bin固件下载地址](https://github.com/spacehuhn/esp8266_deauther/releases)
1. 其中OLed为带显示屏的固件
2. flasher在设置中选择bin文件点击刷入即可,具体[操作步骤](https://github.com/nodemcu/nodemcu-flasher)

### 3.使用Arduino刷入源码

下载完源码之后，大家可能会问：源码难道不是要编译之后才能用吗？没错，我们今天要用到的编译器就是Arduino IDE。进入Arduino官网，[下载](https://www.arduino.cc/en/main/software)最新的Arduino IDE。
Arduino IDE针对不同的系统有不同的版本 Windows也有安装版和绿色版可选Arduino IDE的安装也非常简单，一路下一步就可以了。安装完成后，电脑里的所有.ino文件就都会链接到Arduino IDE了。接下来，解压刚刚从Github下载的zip，打开里面的esp8266_deauther，再双击打开esp8266_deauther.ino进入IDE后，点击文件-首选项，进入设置界面，然后点击附加开发版管理器网址旁边的小按钮。弹出界面后，将下面两个网址以一行一个的格式粘贴到输入框中，点击确定。
```python
http://arduino.esp8266.com/stable/package_esp8266com_index.json
http://phpsecu.re/esp8266/package_deauther_index.json
```
![b87cff5329de6c475aca35a9b8b53680.png](en-resource://database/5919:1)
就是取消上面的那个小按钮关闭设置界面，打开工具-开发板-开发板管理器，然后搜索esp8266。搜索出两个结果，先安装esp8266，再安装 arduino-esp8266-deauther 。![410b11823d83efcc4a0bc02e04682788.png](en-resource://database/5921:1)
一定要先安装esp8266，再安装arduino-esp8266-deauther下一步，点击工具-开发板，在 ESP8266 Deauther Modules 一栏中找到你使用的开发板，这里我选择的是NodeMCU1.0![723d6b93b52a1eb8c5f9670a1c3560f2.png](en-resource://database/5923:1)
淘宝买到的esp8266板基本都可以选择这个选项好了，准备工作一切就绪，接下来就可以单击项目-上传，将程序上传到你的开发板了。如果提示ArduinoJson错误之类的，请在工具-库管理器内安装5.x版本的ArduinoJson。如果显示刷入失败的话，请按住esp8266上的flash按键，再点击上传，直到上传进度开始为止。上传完成之后，就可以从电脑上拔下来，只使用usb供电了。接下来，我们用手机连接ESP8266生成的wifi，默认wifi名为pwned，密码为deauther，管理界面地址为192.168.4.1进入面板之后，里面有非常多的功能，这里就不多介绍了，反正有中文嘛，自己研究就好啦~下面是几张截图，希望大家不要未经许可攻击别人的设备~
![a1f2f91f7b95551886eeed0361d4676d.png](en-resource://database/5925:1)
首页
![2bd023b2a9fb4aad4b7585cf33ac326a.png](en-resource://database/5927:1)
扫描界面
![ba9ed2ee5f6eaebc6acdb7bf93f06e62.png](en-resource://database/5929:1)
克隆wifi界面
![3b0d8ea1b78867c95f672b05e5748988.png](en-resource://database/5931:1)
攻击页面


## 制作钓鱼wifi
### 刷入固件
[固件和源码下载地址](链接：https://pan.baidu.com/s/195CZ19voXhjBUUnClF7gTw)
提取码：6jf5 
### 将源码上传
用arduino上传web到esp8266到arduino官网下载适合你自己系统的软件：https://www.arduino.cc/en/Main/Software
我的是Windows系统，arduino版本是1.8.4，安装完后打开工具——开发板——开发板管理器，此时会自动更新，过个数分钟更新完毕后（当然，如果用外网的话可能几秒钟就能解决），搜索eps8266，选择第二个，版本选2.2.0，然后安装。

将上面解压后的web源码上传工具的tools放到Arduino根目录里合并，然后返回以下界面，点击文件——新建，新建一个项目，将里面的代码清空，然后点击文件——保存，将项目保存到一个你能找到的位置，点击工具——esp8266 sketch data upload，会出现以下的提示，选择No，会发现新建的项目中多出来一个data文件夹，里面是空的，然后将上面下载的web源码\data里面的三个文件复制到这个文件夹里面。
然后再返回arduino，点击工具，开发板按照自己买的选择，端口选择自己的端口，其他设置如下图红框里面的。
设置完后点击esp8266 sketch data upload，这时不会出现提醒，开始上传web页面，等个1分钟左右esp8266上的蓝灯不闪烁了就表示上传完了。然后电脑连接HH的wifi，浏览器输入192.168.1.1/backdoor.htm
l就能进入web页面了，路由器型号选择通用型，然后输入你测试的wifi编号，点确定，电脑提示SSID伪造成功，手机就会发现出现了个和你测试的wifi一样的没有加密的wifi，原来的HH会不见了，8266的蓝灯常亮，手机连接那个wifi后过几秒会自动弹出路由器升级的页面，然后输入管理员密码，点击开始升级，此时你的esp8266会将管理员密码保存，升级完后，8266的灯就会灭掉。
电脑重新连接HH，进入web页面后管理员密码会在下面的红框这一块显示，这时，就表示获取密码成功了。
到此，整个esp8266制作钓鱼wifi的教程到此结束，祝大家玩的开心！！！
## 参考教程
[esp8226钓鱼测试](https://www.freebuf.com/geek/148996.html)



