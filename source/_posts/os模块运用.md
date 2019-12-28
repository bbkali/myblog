---
title: os模块运用
date: 2019-03-10 13:46:54
tags:
    - Python
    - os
categories:
    - 笔记总结
    - python模块
---
## os模块之路径运用
|方法|说明|
|:----:|:----:|
|os.path.abspath(path)|返回绝对路径|
|os.path.basename(path)|返回文件名|
|os.path.commonprefix(list)|返回list(多个路径)中，所有path共有的最长的路径|
|os.path.dirname(path)|返回文件路径|
|os.path.exists(path)|路径存在则返回True,路径损坏返回False|
|os.path.lexists|路径存在则返回True,路径损坏也返回True|
|os.path.expanduser(path)|把path中包含的"~"和"~user"转换成用户目录|
|os.path.expandvars(path)|根据环境变量的值替换path中包含的"$name"和"${name}"|
|os.path.getatime(path)|返回最近访问时间（浮点型秒数）|
|os.path.getmtime(path)|返回最近文件修改时间|
|os.path.getctime(path)|返回文件path创建时间|
|os.path.getsize(path)|返回文件大小，如果文件不存在就返回错误|
|os.path.isabs(path)|判断是否为绝对路径|
|os.path.isfile(path)|判断路径是否为文件|
|os.path.isdir(path)|判断路径是否为目录|
|os.path.islink(path)|判断路径是否为链接|
|os.path.sismount(path)|判断路径是否为挂载点|
|os.path.join(path1[,path2[,...]])|把目录和文件名合成一个路径|
|os.path.normcase(path)|转换path的大小写和斜杠|
|os.path.normpath(path)|规范path字符串形式|
|os.path.realpath(path)|返回path的真实路径|
|os.path.relpath(path[,start])|从start开始计算相对路径|
|os.path.samefile(path1,path2)|判断目录或文件是否相同|
|os.path.sameopenfile(fp1,fp2)|判断fp1和fp2是否指向同一文件|
|os.path.samestat(stat1,stat2)|判断stat tuple stat1和stat2是否指向同一个文件|
|os.path.split(path)|把路径分割成dirname和basename，返回一个元组|
|os.path.splitdrive(path)|一般用在windows下，返回驱动器名和路径组成的元组|
|os.path.splitext(path)|分割路径，返回路径名和文件扩展名的元组|
|os.path.splitunc(path)|把路径分割为加载点与文件|
|os.path.walk(path,visit,arg)|遍历path，进入每个目录都调用visit函数，visit函数必须有3个参数(arg,dirname,names)，dirname表示当前目录的目录名，names代表当前目录下的所有文件名，args则为walk的第三个参数|
|os.path.supports_unicode_filenames|设置是否支持unicode路径名|
<!-- more -->
### 当前路径
os.getcwd()：查看当前所在路径。
os.listdir(path):列举目录下的所有文件。返回的是列表类型
```python
>>> import os
>>> os.getcwd()
'D:\\pythontest\\ostest'
>>> os.listdir(os.getcwd())
['hello.py', 'test.txt']
```
### 绝对路径
os.path.abspath(path):返回path的绝对路径
```python
>>> os.path.abspath('.')
'D:\\pythontest\\ostest'
>>> os.path.abspath('..')
'D:\\pythontest'
```
### 查看路径的文件夹部分和文件名部分
os.path.split(path):将路径分解为(文件夹,文件名)，返回的是元组类型。可以看出，若路径字符串最后一个字符是\,则只有文件夹部分有值；若路径字符串中均无\,则只有文件名部分有值。若路径字符串有\，且不在最后，则文件夹和文件名均有值。且返回的文件夹的结果不包含\.
os.path.join(path1,path2,...):将path进行组合，若其中有绝对路径，则之前的path将被删除
```python
>>> os.path.split('D:\\pythontest\\ostest\\Hello.py')
('D:\\pythontest\\ostest', 'Hello.py')
>>> os.path.split('.')
('', '.')
>>> os.path.split('D:\\pythontest\\ostest\\')
('D:\\pythontest\\ostest', '')
>>> os.path.split('D:\\pythontest\\ostest')
('D:\\pythontest', 'ostest')
>>> os.path.join('D:\\pythontest', 'ostest')
'D:\\pythontest\\ostest'
>>> os.path.join('D:\\pythontest\\ostest', 'hello.py')
'D:\\pythontest\\ostest\\hello.py'
>>> os.path.join('D:\\pythontest\\b', 'D:\\pythontest\\a')
'D:\\pythontest\\a'
```
 os.path.dirname(path):返回path中的文件夹部分，结果不包含'\'
```python
>>> os.path.dirname('D:\\pythontest\\ostest\\hello.py')
'D:\\pythontest\\ostest'
>>> os.path.dirname('.')
''
>>> os.path.dirname('D:\\pythontest\\ostest\\')
'D:\\pythontest\\ostest'
>>> os.path.dirname('D:\\pythontest\\ostest')
'D:\\pythontest'
```
 os.path.basename(path):返回path中的文件名
 ```python
>>> os.path.basename('D:\\pythontest\\ostest\\hello.py')
'hello.py'
>>> os.path.basename('.')
'.'
>>> os.path.basename('D:\\pythontest\\ostest\\')
''
>>> os.path.basename('D:\\pythontest\\ostest')
'ostest'
 ```
### 查看文件时间
 os.path.getmtime(path):文件或文件夹的最后修改时间，从新纪元到访问时的秒数。
 os.path.getatime(path):文件或文件夹的最后访问时间，从新纪元到访问时的秒数。
 os.path.getctime(path):文件或文件夹的创建时间，从新纪元到访问时的秒数
 ```python
>>> os.path.getmtime('D:\\pythontest\\ostest\\hello.py')
1481695651.857048
>>> os.path.getatime('D:\\pythontest\\ostest\\hello.py')
1481687717.8506615
>>> os.path.getctime('D:\\pythontest\\ostest\\hello.py')
1481687717.8506615
 ```
### 查看文件大小
os.path.getsize(path):文件或文件夹的大小，若是文件夹返回0
```python
>>> os.path.getsize('D:\\pythontest\\ostest\\hello.py')
58L
>>> os.path.getsize('D:\\pythontest\\ostest')
0L
```
### 查看文件是否存在
os.path.exists(path):文件或文件夹是否存在，返回True 或 False
```python
>>> os.listdir(os.getcwd())
['hello.py', 'test.txt']
>>> os.path.exists('D:\\pythontest\\ostest\\hello.py')
True
>>> os.path.exists('D:\\pythontest\\ostest\\Hello.py')
True
>>> os.path.exists('D:\\pythontest\\ostest\\Hello1.py')
False
```
## os模块之属性运用
|方法|说明|
|:----:|:----:|
|os.sep|取代操作系统特定的路径分隔符|
|os.extsep|.|
|os.pathsep|;|
|os.linesep|\r\n|
|os.name|指示你正在使用的工作平台。比如对于Windows，它是'nt'，而对于Linux/Unix用户，它是'posix'|
|os.getcwd|得到当前工作目录，即当前python脚本工作的目录路径。|
|os.getenv()和os.putenv|分别用来读取和设置环境变量|
|os.listdir()|返回指定目录下的所有文件和目录名|
|os.remove(file)|删除一个文件|
|os.stat（file）|获得文件属性|
|os.chmod(file)|修改文件权限和时间戳|
|os.mkdir(name)|创建目录|
|os.rmdir(name)|删除目录|
|os.removedirs（r“c：\python”）|删除多个目录|
|os.system()|运行shell命令|
|os.exit()|终止当前进程|
|os.linesep|给出当前平台的行终止符。例如，Windows使用'\r\n'，Linux使用'\n'而Mac使用'\r'|
|os.listdir(dirname)|列出dirname下的目录和文件|
|os.getcwd()|获得当前工作目录|
|os.curdir|返回当前目录（'.'）|
|os.chdir(dirname)|改变工作目录到dirname|
|os.urandom(位数)|产生随机位数|

## os模块之常用笔记
1. 获取当前脚本的绝对路径
```python
os.path.abspath(__file__)
```
2. 重组、拆开路径
```python
# split方法
>>> os.path.split('D:\\pythontest\\ostest\\Hello.py')
('D:\\pythontest\\ostest', 'Hello.py')
# splitext方法
>>> os.path.splitext('D:\\pythontest\\ostest\\Hello.py')
('D:\\pythontest\\ostest\\Hello', '.py')
# join方法
>>> os.path.join('D:\\pythontest\\ostest','hello.py')
'D:\\pythontest\\ostest\\hello.py'
```
3. 过滤特定后缀名文件
```python
# 方法一 通过endswith遍历后缀为ppt和pptx的文件
[fns for fns in os.listdir(os.path.dirname(os.path.abspath('.'))) if fns.endswith(('ppt','pptx'))]
# 方法二 通过os.split方法遍历后缀为ppt和pptx的文件
fns for fns in os.listdir(os.path.dirname(os.path.abspath('.'))) if os.path.splitext(fns)[-1] in ('ppt','pptx')
```

## 实例运用
1. 递归搜寻特定后缀文件
```python
import os
tmpdir = os.listdir('.')
geshi=['.png','.PNG','jpg','doc','docx','xls','xlsx','txt']
bashpath = os.path.dirname(os.path.abspath(__file__))
def search_dir(dir_path,geshi,deeps=1):
    for i in os.listdir(dir_path):
        if os.path.isdir(os.path.join(dir_path,i)):
            final2_path=os.path.join(dir_path,i)
            deeps+=1
            search_dir(final2_path,geshi,deeps)
        else:
            if os.path.splitext(i)[-1] in geshi:
                print(deeps*'\t'+'[+]',os.path.join(dir_path,i))
                return os.path.join(dir_path,i)
            else:
                pass

for i in tmpdir:
    if os.path.isdir(i):
        final_path=os.path.join(bashpath,i)
        print('--',final_path)
        search_dir(final_path,geshi)
    else:
        if os.path.splitext(i)[-1] in geshi:
            print('[+]',os.path.join(bashpath,i))
        else:
            pass
```