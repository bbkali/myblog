---
title: threading模块使用总结
date: 2019-03-11 16:19:39
tags:
    - python
    - threading
categories:
    - 笔记总结
    - threading
---
## threading简介
`线程`是操作系统能够进行运算调度的最小单位。线程被包含在进程中，是进程中实际处理单位。一条线程就是一堆指令集合。一条线程是指进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务.`进程`（process）是一块包含了某些资源的内存区域

## Thread类
Thread是线程类，有两种使用方法，直接传入要运行的方法或从Thread继承并覆盖run()：
```python
# coding:utf-8
import threading
import time
#方法一：将要执行的方法作为参数传给Thread的构造方法
def action(arg):
    time.sleep(1)
    print 'the arg is:%s\r' %arg

for i in xrange(4):
    t =threading.Thread(target=action,args=(i,))
    t.start()

print 'main thread end!'

#方法二：从Thread继承，并重写run()
class MyThread(threading.Thread):
    def __init__(self,arg):
        super(MyThread, self).__init__()#注意：一定要显式的调用父类的初始化函数。
        self.arg=arg
    def run(self):#定义每个线程要运行的函数
        time.sleep(1)
        print 'the arg is:%s\r' % self.arg

for i in xrange(4):
    t =MyThread(i) # 直接可运行run函数
    t.start()

print 'main thread end!'

```
<!--more-->

## 总结概括
|名称|类型|描述|
|:----:|:----:|:----:|
|Thread|类||
| Lock|类||
| Rlock|类||
| Condition|类||
| [Bounded]Semaphore|类||
| Event|类||
| Timer|类||
| local|类||
|threading.currentThread()|常用方法|返回当前的线程变量|
|threading.enumerate()|常用方法|返回一个包含正在运行的线程的list。正在运行指线程启动后、结束前，不包括启动前和终止后的线程|
|threading.activeCount()|常用方法|返回正在运行的线程数量|
|threading.TIMEOUT_MAX|常量|设置threading全局超时时间|

### 构造方法
```python
Thread(group=None, target=None, name=None, args=(), kwargs={}) 

　　group: 线程组，目前还没有实现，库引用中提示必须是None； 
　　target: 要执行的方法； 
　　name: 线程名； 
　　args/kwargs: 要传入方法的参数。
```
### 实例方法 
```bash
　　isAlive(): 返回线程是否在运行。正在运行指启动后、终止前。 
　　get/setName(name): 获取/设置线程名。 
　　start():  线程准备就绪，等待CPU调度
　　is/setDaemon(bool): 获取/设置是后台线程（默认前台线程（False））。（在start之前设置）
"""
如果是后台线程，主线程执行过程中，后台线程也在进行
主线程执行完毕后，后台线程不论成功
与否，主线程和后台线程均停止,如果是前台线程
主线程执行过程中，前台线程也在进行，主线程执行完毕后
等待前台线程也执行完成后，程序停止
"""
　　start(): 启动线程。 
　　join([timeout]): 阻塞当前上下文环境的线程，直到调用此方法的线程终止或到达指定的timeout（可选参数）。
```
 
## 阻塞
### 与主程序并行
```python
# coding:utf-8
import threading
import time

def action(arg):
    time.sleep(1)
    print  '子程序启动!the thread name is:%s\r' % threading.currentThread().getName()
    print 'the arg is:%s\r' %arg
    time.sleep(1)
if __name__ == '__main__':
    
    for i in xrange(4):
        t =threading.Thread(target=action,args=(i,))
        t.start()
    print '主程序完成!'
```
### 阻塞主程序
使多线程编程顺序执行,失去了多线程的意义，不建议使用
```python
# coding:utf-8
import threading
import time

def action(arg):
    time.sleep(1)
    print  '子程序启动!the thread name is:%s\r' % threading.currentThread().getName()
    print 'the arg is:%s\r' %arg
    time.sleep(1)
if __name__ == '__main__':
    
    for i in xrange(4):
        t =threading.Thread(target=action,args=(i,))
        t.start()
        t.join()
    print '主程序完成!'
```
### 阻塞子程序
所有子程序并发完成，然后再执行主程序（常用此方法）
```python
# coding:utf-8
import threading
import time

def action(arg):
    time.sleep(1)
    print  '子程序启动!the thread name is:%s\r' % threading.currentThread().getName()
    print 'the arg is:%s\r' %arg
    time.sleep(1)
if __name__ == '__main__':
    
    for i in xrange(4):
        t =threading.Thread(target=action,args=(i,))
        t.start()
    t.join()
    print '主程序完成!'
```
### 主完子完
只要主线程完成了，不管子线程是否完成，都要和主线程一起退出，这时就可以用setDaemon方法

```python
# coding:utf-8
import threading
import time

def action(arg):
    time.sleep(1)
    print  '子程序启动!the thread name is:%s\r' % threading.currentThread().getName()
    print 'the arg is:%s\r' %arg
    time.sleep(1)
if __name__ == '__main__':
    
    for i in xrange(4):
        t =threading.Thread(target=action,args=(i,))
        t.setDaemon(True)#设置线程为后台线程
        t.start()
    print '主程序完成!'
```
join和setDaemon一起使用
```python
#coding:utf-8
import threading
import time

def action(arg):
    time.sleep(1)
    print  'sub thread start!the thread name is:%s    ' % threading.currentThread().getName()
    print 'the arg is:%s   ' %arg
    time.sleep(1)

thread_list = []    #线程存放列表
for i in xrange(4):
    t =threading.Thread(target=action,args=(i,))
    t.setDaemon(True)
    thread_list.append(t)

for t in thread_list:
    t.start()

for t in thread_list:
    t.join()
```
验证了 join()阻塞当前上下文环境的线程，直到调用此方法的线程终止或到达指定的timeout，即使设置了setDeamon（True）主线程依然要等待子线程结束。


## Lock、Rlock类
```python
"""
由于线程之间随机调度：某线程可能在执行n条后，CPU接着执行其他线程。
为了多个线程同时操作一个内存中的资源时不产生混乱
我们使用锁。Lock（指令锁）是可用的最低级的同步指令
Lock处于锁定状态时，不被特定的线程拥有
Lock包含两种状态——锁定和非锁定，以及两个基本的方法。
可以认为Lock有一个锁定池，当线程请求锁定时，将线程至于池中
直到获得锁定后出池。池中的线程处于状态图中的同步阻塞状态。
RLock（可重入锁）是一个可以被同一个线程请求多次的同步指令。
RLock使用了“拥有的线程”和“递归等级”的概念
处于锁定状态时，RLock被某个线程拥有。
拥有RLock的线程可以再次调用acquire()
释放锁时需要调用release()相同次数。
可以认为RLock包含一个锁定池和一个初始值为0的计数器
每次成功调用acquire()/release()，计数器将+1/-1
为0时锁处于未锁定状态。
"""
```
简言之：__Lock属于全局，Rlock属于线程__。
构造方法： Lock()，Rlock（）,推荐使用Rlock()

实例方法： 
　　acquire([timeout]): 尝试获得锁定。使线程进入同步阻塞状态。 
　　release(): 释放锁。使用前线程必须已获得锁定，否则将抛出异常。

例子一（未使用锁）：
```python
import time
import threading

def addNum():
    global num #在每个线程中都获取这个全局变量
    # num-=

    temp=num
    print('--get num:',num )
    #time.sleep(0.1)
    num =temp-#对此公共变量进行-1操作


num = 1#设定一个共享变量
thread_list = []
    for i in range(100):
    t = threading.Thread(target=addNum)
    t.start()
    thread_list.append(t)

for t in thread_list: #等待所有线程执行完毕
    t.join()

print('final num:', num )
```

例子二（使用锁）:
```python
import time
import threading

def addNum():
    global num #在每个线程中都获取这个全局变量
    # num-=
    lock.acquire()　　　　#加同步锁
    temp=num
    print('--get num:',num )
    #time.sleep(0.1)
    num =temp-#对此公共变量进行-1操作
    lock.release()　　　　#解锁

num = 1#设定一个共享变量
thread_list = []
lock=threading.Lock()　　#创建lock对象

for i in range(100):
    t = threading.Thread(target=addNum)
    t.start()
    thread_list.append(t)

for t in thread_list: #等待所有线程执行完毕
    t.join()　　　　　　#所有线程执行完后主程序才能结束

print('final num:', num )
可以看出，全局变量在在每次被调用时都要获得锁，才能操作，因此保证了共享数据的安全性
```
Lock对比Rlock,死锁解决办法
```python
lockA=threading.Lock()
lockB=threading.Lock()
lock = threading.Rlock()
```
## 生产者消费者模型
引入queue模块，解决锁的问题
```python
# coding:utf-8
import threading
from queue import Queue
import time

q = Queue()
def action(arg):
    time.sleep(1)
    print  '子程序启动!the thread name is:%s\r' % threading.currentThread().getName()
    print 'the arg is:%s\r' %arg
    time.sleep(1)
def work():
    while q.qsize()>0:
        # 将数据从queue中取出来
        arg=q.get()
        action(arg)

if __name__ == '__main__':
    threadscount=4
    data=[1,2,3,4]
    # 将要传入的参数添加到queue中
    for i in data:
        q.put(i)
    for i in range(threadscount):
        t =threading.Thread(target=work)
        t.start()
    print '主程序完成!'
```

参考文章链接:https://www.cnblogs.com/tkqasn/p/5700281.html