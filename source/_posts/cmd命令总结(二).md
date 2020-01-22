---
 title: cmd命令总结(二)
 date: 2020-01-22 15:09:04
 tags: Scrapy
 categories: Scrapy
---

### 重定向操作符

**>**  输出
**>>** 增加输出
**<** 输入

**句柄的数字代码描述**
* 0：键盘输入
* 1：输出到命令提示符窗口
* 2：错误输出

> di >right.txt 2>error.txt # 如果错误就输出到error.txt中去

### 网络命令

**ping**
```
ping host -t # 一直ping
ping host -n count # 发送包量
```

**ifconfig**
```
ipconfig /all # 查看所有网络配置信息
ipconfig /renew # 更新所有适配器,重新获取ip
ipconfig /release # 释放ip
ipconfig /flushdns # 刷新dns
```

**netstat** 显示当前网络协议连接情况

> netstat -ano # 查看当前连接情况

**net** 
1. net user # 查看用户信息
2. lusrmgr.msc # 图形用户管理
3. net localgroup # 本地用户组管理
4. net share
> net share f=F:/ 共享F盘
> net share f /delete 取消共享
> net start + 服务 # 开启服务

**netsh**

> netsh dump > 路径 # 备份所有的网络设置

> netsh int ip # 查看网卡设置
> set address name="wlan" source=static addr=192.168.0.2 mask=255.255.255.0 gateway=192.168.0.1 # 设置静态ip
> set address name="wlan" source=dhcp # 自动获取ip


> netsh winsock reset # 重新安装网络协议ping本地不通时

> netsh firewall set opmode mode=enable # 开启防火墙

> netsh wlan show profiles # 查看当前系统已经保存的网络

> netsh wlan show profiles name="ssid" key=clear | findstr 关键内容# 查看某个ssid的信息其中含密码

### 高级命令

attrib
> type nul>1.txt # 创建空文件
> attrib +h 1.txt # 将文件设置为隐藏

**goto** 跳转函数
```
goto part1

:part1
echo test
exit
```

**call** 调用程序
> call demo.bat

**start** 打开程序


### 案例
1. 在内网ping查找存活主机
> for /l %%i in (1,1,255) do ping -n 1 192.168.0.%i | findstr 字节 >> iplist.txt

2. 批量创建文件或文件夹
> for /l %%i in (1,1,100) do md 新建文件夹%i

3. 批量找出特定后缀的文件名
> for /r  f:\ %i in (*.txt) do echo %%i
