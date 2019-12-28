---
title: time模块运用
date: 2019-03-10 12:57:36
tags:
    - python
    - time
categories:
    - 笔记总结
    - python模块
---

## time表现格式
1. `timestamp时间戳`，时间戳表示的是从1970年1月1日00:00:00开始按秒计算的偏移量
2. `struct_time时间元组`，共有九个元素组
3. `format_time格式化时间`，已格式化的结构使时间更具可读性。包括自定义格式和固定格式

## time格式转化实例
[![A9JuD0.png](https://s2.ax1x.com/2019/03/10/A9JuD0.png)](https://imgchr.com/i/A9JuD0)
<!--more-->
### 导入模块
```python
import time
```
### 生成时间戳
```python
# t1为当前时间戳 1552223079.937368
t1 = time.time()
```
### 生成时间元组
```python
# t2为当前时间元组 time.struct_time(tm_year=2019, tm_mon=3, tm_mday=10, tm_hour=21, tm_min=5, tm_sec=35, tm_wday=6, tm_yday=69, tm_isdst=0)
t2 = time.localtime() 
```
### 生成格式化时间
```python
# t3为当前默认格式化时间 Sun Mar 10 21:12:55 2019
t3 = time.ctime()
```
### 转化实例
* 时间戳、时间元组互相转化
```python
# 时间戳 -- > 时间元组
t1 = time.time()
time.localtime(t1)
time.gtime(t1)

# 时间元组 -- > 时间戳
t2 = time.localtime()
time.mktime(t2)
```
* 格式化时间、时间元组互相转化
```python
# 时间元组 -- > 格式化时间
t3 = time.strftime(t2)
t3 = time.asctime(t2)
# 格式化时间 -- > 时间元组
time.strptime(t3)
```
* 时间戳、格式化时间互相转化
```python
# 时间戳 -- > 格式化时间
time.ctime(t1)
```

### 函数借鉴
```python
import time
 
#生成当前时间的时间戳，只有一个参数即时间戳的位数，默认为10位，输入位数即生成相应位数的时间戳，比如可以生成常用的13位时间戳
def now_to_timestamp(digits = 10):
    time_stamp = time.time()
    digits = 10 ** (digits -10)
    time_stamp = int(round(time_stamp*digits))
    return time_stamp
 
#将时间戳规范为10位时间戳
def timestamp_to_timestamp10(time_stamp):
    time_stamp = int (time_stamp* (10 ** (10-len(str(time_stamp)))))
    return time_stamp
 
#将当前时间转换为时间字符串，默认为2017-10-01 13:37:04格式
def now_to_date(format_string="%Y-%m-%d %H:%M:%S"):
    time_stamp = int(time.time())
    time_array = time.localtime(time_stamp)
    str_date = time.strftime(format_string, time_array)
    return str_date
 
#将10位时间戳转换为时间字符串，默认为2017-10-01 13:37:04格式
def timestamp_to_date(time_stamp, format_string="%Y-%m-%d %H:%M:%S"):
    time_array = time.localtime(time_stamp)
    str_date = time.strftime(format_string, time_array)
    return str_date
 
#将时间字符串转换为10位时间戳，时间字符串默认为2017-10-01 13:37:04格式
def date_to_timestamp(date, format_string="%Y-%m-%d %H:%M:%S"):
    time_array = time.strptime(date, format_string)
    time_stamp = int(time.mktime(time_array))
    return time_stamp
 
#不同时间格式字符串的转换
def date_style_transfomation(date, format_string1="%Y-%m-%d %H:%M:%S",format_string2="%Y-%m-%d %H-%M-%S"):
    time_array  = time.strptime(date, format_string1)
    str_date = time.strftime(format_string2, time_array)
    return str_date
 
```
## 时间元组(struct_time)元素结构
|属性|值|
|:----|:----:|
|tm_year（年）|比如2011|
|tm_mon（月）|1 - 12|
|tm_mday（日）|1 - 31|
|tm_hour（时）|0 - 23|
|tm_min（分）|0 - 59|
|tm_sec（秒）|0 - 61|
|tm_wday（weekday）|0 - 6（0表示周日）|
|tm_yday（一年中的第几天）|1 - 366|
|tm_isdst（是否是夏令时）|默认为-1|

## 格式化时间(format time)结构化表示
|格式|含义|
|:----:|:----:|
|%a|本地（locale）简化星期名称|
|%A|本地完整星期名称|
|%b|本地简化月份名称|
|%B|本地完整月份名称|
|%c|本地相应的日期和时间表示|
|%d|一个月中的第几天（01-31）|
|%H|一天中的第几个小时（24小时制，00-23）|
|%I|第几个小时（12小时制，01-12）|
|%j|一年中的第几天（001-366）|
|%m|月份（01-12）|
|%M|分钟数（00-59）|
|%p|本地am或者pm的相应符|
|%S|秒（01-61）|
|%U|一年中的星期数。（00-53星期天是一个星期的开始。）第一个星期天之前的所有天数都放在第0周。|
|%w|一个星期中的第几天（0-6，0是星期天）|
|%W|和%U基本相同，不同的是%W以星期一为一个星期的开始。|
|%x|本地相应日期|
|%X|本地相应时间|
|%y|去掉世纪的年份（00-99）|
|%Y|完整的年份|
|%Z|时区的名字（如果不存在为空字符）|
|%%|‘%’字符|
## 常用补充
1. time中有sleep函数,休眠时间，单位秒(s)
```python
from time import sleep
sleep(2) # 休眠2s再运行程序
```
2. 时间差计算,一般用时间戳来运算
```python
import time
t1 = time.time()
# 主程序运行
run()
t2 = time.time()
print('总共运行了%s秒'%t2-t1)
```
3. 格式化当前时间
```python
import time
time.strftime("%Y-%m-%d %X",time.localtime())
```