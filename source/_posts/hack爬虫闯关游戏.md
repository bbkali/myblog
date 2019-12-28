---
title: hack爬虫闯关游戏
date: 2019-01-26 22:04:01
tags: 
- python
- 爬虫
categories: 
- 实践运用
- 爬虫
---
__无意看到一个很好玩的爬虫闯关游戏,后续会持续将每关心得贴出来__
[游戏开始地址](http://www.heibanke.com/lesson/crawler_ex00/)
## 第一关
访问链接得到可以看到如下题目
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-dbc8ae1f13a8e511.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 测试思路：按提示将数字添加倒网址的后方
<!--more-->
例如：http://www.heibanke.com/lesson/crawler_ex00/75618
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-82e3b2c2048ef6db.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 测试思路：发现会有新的数字跳转，同理将数字替换之前的网址上
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-3c1a14debac6d240.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 测试思路： 由此可以猜测出该关玩法应该是，将网页显示的数字添加到网页url上，直到找到成功的为止
由此开始写代码：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Date    : 2017-08-26 19:19:03
# @Word    : python can change world!
# @Version : python3.6

import requests
import re
from bs4 import BeautifulSoup

def get_html(page):
    url='http://www.heibanke.com/lesson/crawler_ex00/'+str(page)
    res=requests.get(url).text
    return res

def main():
    page=''
    print('开始第一关')
    while True:
        req=get_html(page)
        if u'恭喜' in req:
            print('成功啦！可以试着将最后显示的数字填进网址里去看看哦！')
            break
        else:
            soup=BeautifulSoup(req,"html.parser")
            html=soup.h3.string
            num=re.findall(r'\d+',html)
            page=str(num.pop())
            print(page)


if __name__ == '__main__':
    main()

```
__成功后可以获取下一关的地址链接哦！试试吧，如果你也对python爬虫有兴趣，欢迎交流指证哦！继续第二关的爬虫闯关游戏__[下一关地址](http://www.heibanke.com/lesson/crawler_ex01/)

## 第二关

访问链接得到可以看到如下题目
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-5b4ebf8b99014376.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 测试思路：按提示随便写个昵称和30以内数字的密码
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-6369aff107f44cde.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 测试思路：发现密码错误，由此可直到需要将1-30为密码遍历下去，直到成功！

__`代码思路`：通过request模块的post提交data参数,通过re模块抓取成功后的信息！就是这么简单!__

由此开始写代码：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Date    : 2017-08-26 22:48:40
# @Word    : python can change world!
# @Version : python3.6
import requests
import re
def main():
    url='http://www.heibanke.com/lesson/crawler_ex01/'
    num=0
    a=30
    while a >0:
        data={'username':'admin','password':num}
        res=requests.post(url,data=data).text
        if u'错误' in res:
            print(num,'错误')
            num=num+1
            a=a+1
        else:
            print(num,'正确')
            title=re.findall("<title>(.*?)</title>",res)
            word=re.findall("<h1>(.*?)</h1>",res)
            word2=re.findall("<h3>(.*?)</h3>",res)
            html=re.findall('<a href="(.*?)" class="btn btn-primary">下一关</a>',res)
            print('\n'.join([title[0], word[0], word2[0],'下一关地址是','http://www.heibanke.com'+html[0]]))
            break

if __name__ == '__main__':
    main()

```
>
![过关标识](http://upload-images.jianshu.io/upload_images/4838174-c47fbe21c33e72b6.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

__轻松过关有木有！如果你也对python爬虫有兴趣，欢迎交流指证哦！继续第三关的爬虫闯关游戏__[下一关地址](http://www.heibanke.com/lesson/crawler_ex02/)

## 第三关
访问链接得到可以看到如下题目:
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-e9169f32a091818c.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 测试思路：页面跳转到http://www.heibanke.com/accounts/login/?next=/lesson/crawler_ex02/,看来要先注册一个账号
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-a50712dd66a9b59b.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 测试思路：注册一个账号，登陆进去,发现一个记账点，但网页还没有跳转到题目网页，
还不知道玩法如何
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-7797ba57b6a62ffd.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 测试思路： 因为注册账号登陆了，重新访问下刚开始的题目地址http://www.heibanke.com/lesson/crawler_ex02/，发现可以开始玩了
 ![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-da96b994b5add913.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 测试思路： 很明显长的和第二关有点像，但题目说多了两层保护，是哪两层呢？
首先，多了账号登陆一层，还有一层是什么呢？重新登陆，打开firebug测试一下
>![登录时的post表单参数](http://upload-images.jianshu.io/upload_images/4838174-c42735708781d72f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5. 测试思路： 所以第一层request模块post访问，还有一个csrfmiddlewaretoken参数，可csrfmiddlewaretoken参数如何获得呢？是随机的还是固定的？回到登陆界面再探个究竟！
>![csr参数](http://upload-images.jianshu.io/upload_images/4838174-054dd6a4d5e995c1.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. 测试思路： 很明显每次在访问登录界面时会自动生成csrfmiddlewaretoken，是随机的！登录进去找第二层！同理开着firebug刷新页面，看看是不是也是有别的参数带入
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-2b53a76c7fa0787a.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7. 测试思路： 好吧，找到了在闯关登录界面，也会自动生成一个csr参数，不过刷新了几次都没变
很大可能是固定了，或许这就是第二层保护吧！

__`代码思路`：由此总结可知，通过request模块访问login页面，获取csr随机参数,再带入账号密码一起post提交表单登入，登入之后再获取csr固定参数，再带入账号，和随机的30以内的数字密码，只要密码对了就过关了！__

由此开始写代码：
```python
import requests
import re
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Date    : 2017-08-26 22:48:40
# @Word    : python can change world!
# @Version : python3.6

def main():
    login_data={'username':'user','password':'password'}
    url='http://www.heibanke.com/lesson/crawler_ex02/'
    login_url='http://www.heibanke.com/accounts/login/?next=/lesson/crawler_ex02/'
    r2=requests.get(login_url)
    c2=r2.cookies
    login_data['csrfmiddlewaretoken']=c2['csrftoken']
    r3=requests.post(login_url,data=login_data,allow_redirects=False,cookies=c2)
    c3=r3.cookies
    pass_data={'username':'user','csrfmiddlewaretoken':c3['csrftoken']}
    for passwd in range(31):
        pass_data['password']=pas
        swd
        r5=requests.post(url,pass_data,cookies=c3)
        text=r5.text
        result=re.findall(r'密码错误',text)
        if u'密码错误' in text:
            print("%s密码错误"%passwd)
        else:
            print("%s密码正确"%passwd)
            title=re.findall("<title>(.*?)</title>",text)
            word=re.findall("<h1>(.*?)</h1>",text)
            word2=re.findall("<h3>(.*?)</h3>",text)
            html=re.findall('<a href="(.*?)" class="btn btn-primary">下一关</a>',text)
            print('\n'.join([title[0], word[0], word2[0],'下一关地址是','http://www.heibanke.com'+html[0]]))
            break
      
if __name__ == '__main__':
    main()

```
>
![过关标识](http://upload-images.jianshu.io/upload_images/4838174-9d759ab1b61c1095.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

__轻松过关有木有！如果你也对python爬虫有兴趣，欢迎交流指证哦！继续第四关的爬虫闯关游戏__[下一关地址]：(http://www.heibanke.com/lesson/crawler_ex03/)

## 第四关

访问链接得到可以看到如下题目

>![图片.png](http://upload-images.jianshu.io/upload_images/4838174-00734ba202fe7cdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 测试思路：看来和第三关第一步一样，都是要先登录哟！随便输个密码试试，毕竟还不知道密码多少位，怎么玩
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-a50712dd66a9b59b.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 测试思路： 密码错误会提示进入一个链接，点击发现有一个列表，是位置和密码值的对应，而且刷新一下竟然会变！猜测是随机产生，应该可以就单独一页进行密码爬取！再组成最后的密码
![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-0dec3d195aaa76c0.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![捕获2.PNG](http://upload-images.jianshu.io/upload_images/4838174-ae5abef901702ab3.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


3. 测试思路： 翻页发现没有超过100 的数字，估计密码最大就是100位吧，而且访问过程特别慢
可以使用多线程进行快速抓取！
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-d51d41517b031271.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>__`代码思路`：由此总结可知，通过request模块访问login页面，获取csr随机参数,再带入账号密码一起post提交表单登入，登入之后访问密码列表，爬取密码位置，和对应数值，再组成100位的密码，再带入账号密码登录，只要密码对了就过关了！__

由此开始写代码：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Date    : 2017-09-02 22:25:21
# @Word    : python can change world!
# @Version : python3.6
import requests
from bs4 import BeautifulSoup
import threading
from queue import Queue


dict1={}
vlauess=[]
web1="http://www.heibanke.com/accounts/login"
web2="http://www.heibanke.com/lesson/crawler_ex03/pw_list/"
web3="http://www.heibanke.com/lesson/crawler_ex03/"
global queuewz
global queuemm
queuewz=Queue()
queuemm=Queue()


class mythreads(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        work()
        while not queuemm.empty():
            try:
                dict1[str(queuewz.get())]=queuemm.get()
                print(dict1)
                print("字典长度为%s"%len(dict1))
                if int(len(dict1)) ==100:
                    print("凑到100啦！")
                    for i in range(1,101):
                        vlauess.append(dict1[str(i)])
                    c=vlauess[:100]
                    zzmm=''.join(c)
                    print("密码为%s"%zzmm)
                    print("正在登录.......")
                    dataWebsite1 = {'username': 'user','password': zzmm}
                    s=login_get()
                    res=s.post(web3, data=dataWebsite1).text
                    if u'恭喜' in res:
                        title=re.findall("<title>(.*?)</title>",res)
                        word=re.findall("<h1>(.*?)</h1>",res)
                        word2=re.findall("<h3>(.*?)</h3>",res)
                        html=re.findall('<a href="(.*?)" class="btn btn-primary">下一关</a>',res)
                        print('\n'.join([title[0], word[0], word2[0],'下一关地址是','http://www.heibanke.com'+html[0]]))
                        break
                    else:
                        print("网页有问题哦！可以尝试手动将获得的正确密码登入进去哦！")
                        break
                else:
                    main()
            except IndexError:
                print("例表空了,下一页！")


def login_get():
    try:
        s = requests.Session()
        r=s.get(web1)     # 访问登录页面获取登录要用的csrftoken
        token1 = r.cookies['csrftoken']      # 保存csrftoken
        # 将csrftoekn存入字段csrfmiddlewaretoken
        dataWebsite1 = {'username': 'user',
                        'password': 'password',
                        'csrfmiddlewaretoken': token1
                    }
        res=s.post(web1, data=dataWebsite1)
    except KeyError as e:
        pass

    return s

def get_html(s):
    r=s.get(web2)
    res=r.text
    return res

def get_dict(res):

    soup=BeautifulSoup(res,"html.parser")
    for a in soup.find_all('td',attrs={'title':'password_pos'}):
        wz=(a.string)
        queuewz.put(wz)
    for b in soup.find_all('td',attrs={'title':'password_val'}):
        mm=(b.string)
        queuemm.put(mm)

def work():
    res=get_html(s)
    get_dict(res)


def main():
    global s
    s=login_get()
    threads=[]
    threads_count=10

    for i in range(threads_count):
        threads.append(mythreads())

    for t in threads:
        t.start()

    for t in threads:
        t.join()


if __name__ == '__main__':
    main()




```

__账号为user,密码可得为：9263152516931477925579303326531490163668774566288173618538253675264972338183086744513535774895069524__

>![登录过关标识](http://upload-images.jianshu.io/upload_images/4838174-70f637bd25721d5f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里线程我给的是10，访问速度不算快，但比多线程快很多哦，写的有点杂乱,继续第五关的爬虫闯关游戏[下一关地址]：(http://www.heibanke.com/lesson/crawler_ex04/)

## 第五关

访问链接可以看到如下视图：
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-e5e276210ec84884.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 测试思路：看来和第四关第一步一样，都是要先登录哟！先建个session,然后到需要添加验证码的这个页面，手动随便输入账号密码firebug抓包看看表单参数
>![捕获.PNG](http://upload-images.jianshu.io/upload_images/4838174-63ae81eaa8752a0f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 测试思路： username和password是需要输入的，captha_1为验证码，看来需要用到ocr来识别验证码，csr参数是登录开始时就可以获取，captha_0参数可以在源码中找到会随机变化

3. 测试思路： 手动将验证码另存为png图片，验证码不会刷新，看来可以用下载验证码再识别的方法，而不需要截图页面读取验证码，这里需要安装第三方tesseract-ocr模块，同时要训练机器学习识别验证码，具体识别需要的软件和步骤，[可以点击这里查看](http://www.jianshu.com/p/5c8c6b170f6f)

4. 测试思路： 需要从题目中下载大概100个样本，给机器学习识别，具体代码如下:
```python
import requests
import re
import urllib.request

web_login="http://www.heibanke.com/accounts/login/?next=/lesson/crawler_ex04/"
web_ex04="http://www.heibanke.com/lesson/crawler_ex04/"
def get_s():
    s=requests.Session()
    s.get(web_login)
    token1 = s.cookies['csrftoken']      # 保存csrftoken
    # 将csrftoekn存入字段csrfmiddlewaretoken
    dataWebsite1 = {'username': 'user',
                    'password': 'password',
                    'csrfmiddlewaretoken': token1
                    }
    s.post(web_login, data=dataWebsite1)
    return s
    
def get_img(s):
    for i in range(1,101):
        res=s.get(web_ex04).text
        img=re.findall('![]((.*?))',res)
        img_html='http://www.heibanke.com'+str(img[0])
        print("这是第%s张验证码"%i)
        urllib.request.urlretrieve(img_html,"%s.png"%i)
        # print(img_html)

def main():
    s=get_s()
    get_img(s)


if __name__ == '__main__':
    main()
```

5. 测试思路： 通过tesseract可以对样本依次进行识别，如果机器没有学习，默认字体为eng，识别率惨不忍睹，所以可以用jTessBoxEditor软件对tesseract识别的答案进行纠正保存，提高识别率，这就是机器学习的过程。然后将训练过程生成一个num字体文件，用tesseract调用num字体进行识别。具体识别需要的软件和步骤，[可以点击这里查看](http://www.jianshu.com/p/5c8c6b170f6f)

以下是需要用到的cmd命令我把它整理成bat文件
```bash
rem 执行改批处理前先要目录下创建font_properties文件 ，内容为font 0 0 0 0 0

echo Run Tesseract for Training.. 
tesseract.exe num.font.exp0.tif num.font.exp0 nobatch box.train 
 
echo Compute the Character Set.. 
unicharset_extractor.exe num.font.exp0.box 
mftraining.exe -F font_properties -U unicharset -O num.unicharset num.font.exp0.tr 

echo Clustering.. 
cntraining.exe num.font.exp0.tr 

echo Rename Files.. 
rename normproto num.normproto 
rename inttemp num.inttemp 
rename pffmtable num.pffmtable 
rename shapetable num.shapetable  

echo Create Tessdata.. 
combine_tessdata.exe num. 

echo. & pause
```

* 需要用到的命令：
tesseract num.font.exp0.tif num.font.exp0 batch.nochop makebox
1.先创建font_properties文件 ，内容为font 0 0 0 0 0
2.执行如上批处理文件生成num.tessdata字体文件
3.将字体文件复制到tesseract字体目录下
4.在cmd中tesseract --list-langs 可以看到新的num文件
具体内容可以[点击这里](http://www.cnblogs.com/cnlian/p/5765871.html)

__`代码思路`：由此总结可知，通过request模块访问login页面，获取csr随机参数,再带入账号密码一起post提交表单登入，登入之后下载验证码用pytesseract模块调用tesseract识别验证码，并提交给captha_1参数，再加上csr和captha_0参数以post方式访问，password从0开始遍历数字，只要密码对了就过关了！__

由此开始写代码：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Date    : 2017-09-11 12:25:25
# @word : python will change the world!
# @Version : 3.6

import requests
import re
import urllib.request
import pytesseract
from PIL import Image
import sys
web_login="http://www.heibanke.com/accounts/login/?next=/lesson/crawler_ex04/"
web_ex04="http://www.heibanke.com/lesson/crawler_ex04/"

def get_s():
    s=requests.Session()
    s.get(web_login)
    token1 = s.cookies['csrftoken']      # 保存csrftoken
    dataWebsite1 = {'username': 'user',
                    'password': 'password',
                    'csrfmiddlewaretoken': token1
                    }
    s.post(web_login, data=dataWebsite1)
    return s

def cleanyzm(yzm,s,num):
    a=yzm
    if len(a.replace(' ',''))!=4:
        print("打回")
        get_img(s,num)
    else:
        yzm=a.upper()
        print(yzm)
        return yzm

def get_img(s,num):
    res=s.get(web_ex04).text
    img=re.findall('![]((.*?))',res)
    img_html='http://www.heibanke.com'+str(img[0])
    print("验证码下载完毕")
    urllib.request.urlretrieve(img_html,"yzm.png")
    yzm=pytesseract.image_to_string(Image.open('yzm.png'),lang='num')
    cshu=re.findall(r'img src="/captcha/image/(.*)/" alt="captcha"', res)[0]
    token1 = s.cookies['csrftoken']
    yzm=cleanyzm(yzm,s,num)
    dataWebsite1 = {'username': 'user',
                        'password': num,
                        'csrfmiddlewaretoken': token1,
                        'captcha_0':cshu,
                        'captcha_1':yzm
                        }
    res=s.post(web_ex04, data=dataWebsite1).text
    if u'密码错误' in res:
        print(num,"密码错误")
        num=num+1
        get_img(s,num)
    else:
        if u'验证码输入错误' in res:
            print(num,"验证码输入错误！")
            get_img(s,num)
        else:
            print(num,"成功！")
            title=re.findall("<title>(.*?)</title>",res)
            word=re.findall("<h1>(.*?)</h1>",res)
            word2=re.findall("<h3>(.*?)</h3>",res)
            print('\n'.join([title[0],word[0],word2[0]]))
            sys.exit(0)

def main():
    s=get_s()
    #global num
    num=0             ########设置开始运行的密码，直到成功程序退出！
    get_img(s,num)


if __name__ == '__main__':
    main()

```

__账号为user密码可得为一个二位数字__

![登录过关标识](http://upload-images.jianshu.io/upload_images/4838174-f5f8ef451cef756f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

__这里我用的是pytesseract模块，识别率也还不是很理想，不断循环直至验证码读对为止，还好下载验证码不会刷新，不然就只能用selenium模块进行操控浏览器进行模拟登陆了，这也不乏一种思路哦！感兴趣的朋友可以试试，总体感觉闯关游戏就第五关相对麻烦一点，花了我将近一天的时间，不过收获很大还是很值得的，如果你也对python爬虫有兴趣，欢迎交流指证，相互学习哦！__         


