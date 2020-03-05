---
 title: 信息搜集之绕过cdn获取真实IP
 date: 2020-02-22 19:17:55
 tags: 信息收集
 categories: 信息收集
---

## CDN介绍
CDN的全称是 Content Delivery Network，即**内容分发网络**，基本思路就是通过在网络各处放置节点服务器所构成的在现有的互联网基础之上的一层智能虚拟网络，CDN核心的就是使用户可就近访问网络,取得所需内容，解决网络拥挤的状况，提高用户访问网站的响应速度或者用户下载速度。一般来说,网站开启CDN之后，会根据用户所在地的不同访问CDN的节点服务器，并不直接访问源服务器，这样可以减少网站服务器宽带资源,降低服务器压力，可以提升用户体验。这也就是大家都在ping百度,但是不同地区得到的反馈ip不一样的原因。其次，由于CDN节点的阻挡防护，可以更好的保护员服务器的安全。具体来说，CDN其实是充当了一个替身的角色，无论服务器是渗透还是DD0S攻击，攻击的目标都将是CDN节点，这样一来便间接的保护了网站本身。
 
## 验证目标网站是否使用了CDN？
<!--more-->
1. 在线超级ping（多地ping）
很简单，使用各种多地 ping 的服务，查看对应 IP 地址是否唯一，如果不唯一，多半是使用了CDN， 多地 Ping 网站有：

http://ping.chinaz.com/
![2020-2-22-19-54-43](https://raw.githubusercontent.com/bbkali/picbad/master/2020-2-22-19-54-43)
http://ping.aizhan.com/
http://ce.cloud.360.cn/
https://www.17ce.com/
 
2. 使用工具检测
使用 nslookup 进行检测，原理同上，如果返回域名解析对应多个 IP 地址多半是使用了 CDN,以baidu为例返回多个地址
> nslookup baidu.com
```
Server:         202.101.224.68
Address:        202.101.224.68#53

Non-authoritative answer:
Name:   baidu.com
Address: 220.181.38.148
Name:   baidu.com
Address: 39.156.69.79
```

## 如何绕过CDN找到目标站点真实IP

### 让其主动连接我们
1. 发邮件给我们：
比如注册的时候会有注册连接发送到我们的邮件，然后查看邮件全文源代码或邮件标头就可以了。如果是大站，会有自己的独立的邮件服务器给你发送，那么这个邮件服务器的有可能跟目标Web在一个段上，我们直接一个一个扫，看返回的HTML源代码是否跟web的对的上。
2. 利用网站漏洞：比如有代码执行漏洞、SSRF、存储型的XSS都可以让服务器主动访问我们预设的web服务器，那么就能在日志里面看见目标网站服务器的真实IP。
 
### 查询DNS历史记录
1. DNS缓存查询：这里主要是利用管理员疏忽，通过DNS缓存查询，查看 IP 与 域名绑定的历史记录，可能会存在使用 CDN 前的记录，相关查询网站有：
https://dnsdb.io/zh-cn/
https://x.threatbook.cn/
http://toolbar.netcraft.com/site_report?url=
http://viewdns.info/
https://tools.ipip.net/cdn.php
 
2. 利用SecurityTrails平台（https://securitytrails.com/），攻击者就可以精准的找到真实原始IP，只需在搜索字段中输入网站域名，然后按Enter键即可，这时“历史数据”就可以在左侧的菜单中找到。
SecurityTrails平台除了过去的DNS记录，即使是当前的记录也可能泄漏原始服务器IP。例如，MX记录是一种常见的查找IP的方式。如果网站在与web相同的服务器和IP上托管自己的邮件服务器，那么原始服务器IP将在MX记录中。

3. 在线网站查询获取
https://get-site-ip.com/

### 查询子域名
毕竟 CDN 还是不便宜的，所以很多站长可能只会对主站或者流量大的子站点做了 CDN，而很多小站子站点又跟主站在同一台服务器或者同一个C段内，此时就可以通过查询子域名对应的 IP 来辅助查找网站的真实IP。
 
下面介绍些常用的子域名查找的方法和工具：
1、https://x.threatbook.cn/
2、https://dnsdb.io/zh-cn/
3、Google 搜索；例如：用语法"site:baidu.com -www"就能查看除www外的子域名。
4、子域名扫描器；这个我就不多说了，这种工具太多了。

### 网络空间搜索引擎
最常见的网络空间搜索引擎有钟馗之眼、shodan、fofa搜索。
 
1、钟馗之眼：https://www.zoomeye.org/
2、Shodan：https://www.shodan.io/
3、FOFA：https://fofa.so/
 
这里主要是利用网站返回的内容寻找真实原始IP，如果原始服务器IP也返回了网站的内容，那么可以在网上搜索大量的相关数据。
 
只需要浏览网站源代码，寻找独特的代码片段。
在JavaScript中使用具有访问或标识符参数的第三方服务（例如Google Analytics、reCAPTCHA、统计）是攻击者经常使用的方法。或者说用title，毕竟竟每个网站的title基本上都是独一无二的。 以fofa为例：可以直接以 title=""来搜索。
再配合最常见的网络空间搜索引擎就可以轻而易举的找到网站的真实的IP。

### 全网扫描
扫描全网开放特定端口的IP，然后获取他们的特定页面的HTM源代码，用这些源代码和目标网站的特定页面的HTM源代码做对比，如果匹配上来了，就很可能是目标网站的真实P，工具匹配会匹配出来很多，最后还是要人工筛选。
 
1. 钟馗之眼、shodan、fofa空间搜索引擎，这里推荐用 https://fofa.so/
第一步：打开fofa；第二步：title="" 得到IP；第三步：再用  ip=="" 做对比。
 
2. 用Zmap扫全网

### 其它一些思路
1. 利用网站敏感信息：比如 phpinfo. php，这个就要看字典了。
2. 国内很多CDN厂商因为各种原因只做了国内的线路，而针对国外的线路可能几乎没有，此时我们使用国外的主机直接访问可能就能获取到真实P。
3. 搞到CDN账号：社工管理员，搞到CDN的账号。(Ps：我都能社这个了，还不能想办法社目标web的后台账号密码？还不能找到真实IP吗？)


