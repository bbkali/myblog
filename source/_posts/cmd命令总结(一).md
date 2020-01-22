---
 title: cmd命令总结(一)
 date: 2020-01-21 18:52:37
 tags: cmd
 categories: cmd
---

## 基本命令

**cd** 进入文件目录

**dir** 列出目录文件
```
/A          显示具有指定属性的文件。
  属性         D  目录                R  只读文件
               H  隐藏文件            A  准备存档的文件
               S  系统文件            I  无内容索引文件
               L  解析点             -  表示“否”的前缀
  /B          使用空格式(没有标题信息或摘要)。
  /C          在文件大小中显示千位数分隔符。这是默认值。用 /-C 来
              禁用分隔符显示。
  /D          跟宽式相同，但文件是按栏分类列出的。
  /L          用小写。
  /N          新的长列表格式，其中文件名在最右边。
  /O          用分类顺序列出文件。
  排列顺序     N  按名称(字母顺序)     S  按大小(从小到大)
               E  按扩展名(字母顺序)   D  按日期/时间(从先到后)
               G  组目录优先           -  反转顺序的前缀
  /P          在每个信息屏幕后暂停。
  /Q          显示文件所有者。
  /R          显示文件的备用数据流。
  /S          显示指定目录和所有子目录中的文件。
  /T          控制显示或用来分类的时间字符域。
  时间段      C  创建时间
              A  上次访问时间
              W  上次写入的时间
  /W          用宽列表格式。
  /X          显示为非 8.3 文件名产生的短名称。格式是 /N 的格式，
              短名称插在长名称前面。如果没有短名称，在其位置则
              显示空白。
  /4          用四位数字显示年
```

<!--more-->

* 常用命令
1. 递归列出该路径下doc结尾的文件
> dir /s /b *.doc 
2. 查看本地路径下存在目录
> dir /aD

* **copy** 复制文件

* **xcopy** 复制目录

* **move** 剪切

* **ren** 重命名

* **replace** 替换
```
replace /a 路径一 路径二  # 把文件添加到新地方，类似复制
```

### 自定义cmd界面
**title** 重名命cmd名称
> title this is test

**mode** 设置窗口大小(宽，高)
> mode 100,50

**colour** 设置cmd的背景和字体颜色
```
0 = 黑色       8 = 灰色
1 = 蓝色       9 = 淡蓝色
2 = 绿色       A = 淡绿色
3 = 浅绿色     B = 淡浅绿色
4 = 红色       C = 淡红色
5 = 紫色       D = 淡紫色
6 = 黄色       E = 淡黄色
7 = 白色       F = 亮白色
```
> "COLOR fc" # 在亮白色上产生淡红色

### 变量

1. 设置变量,以name变量为例
> set name=xxx
2. 打印变量
> set name
3. 释放变量
> set name=

**常用参数**
* set /a 表达式
> set /a var name=4/2
* set /p  命令行开关允许将变量数值设成用户输入的一行输入
```
@echo off
set /p name=请输入一个数字
echo 你输入的数字是:%name%
pause >nul
```

**永久变量设置**
> setx path "%path%;文件夹目录"

### 特殊字符

* **|** 管道符号
* **&** 类似and
* **&&** 前面执行成功才执行后面
* **||** 前面执行失败才执行后面
* **()** 为了隔开美观

```
# dir执行成功打印sucess 否则fales
dir && echo sucess || echo false
```
### 通配符

* ? 代表单个字符
> dir /b ??.mp4 # 列出两个字符名称的mp4文件
* \* 代表全部
> dir /b *.mp4 # 列出所有mp4文件

### 逻辑结构

#### **if**`语法格式`
1. 判断文件或者文件夹是否存在, 用if exist语句
> if exsit f:\1.txt (echo sucess) else (echo false)
2. 判断某两个字符串是否相等。 用if "字符串1" == “字符串2” 语句
> if "1"=="1" (echo sucess) else (echo false)
3. 判断某个变量是否已经被赋值，用if defined str 语句
> if defined name (echo succse)
4. 判断两个数值是否相等，if 数值1 equ 数值2 语句
> if 2 gtr 1 (echo sucess)
* **条件符**
- 等于 equ equal
- 大于 gtr greater than
- 大于或等于 geq
- 小于 less  less than
- 小于或等于 leq
- 不等于 neq


#### **for**`语法格式`
```
FOR %variable IN (set) DO command [command-parameters]

  %variable  指定一个单一字母可替换的参数。
  (set)      指定一个或一组文件。可以使用通配符。
  command    指定对每个文件执行的命令。
  command-parameters
             为特定命令指定参数或命令行开关。

在批处理程序中使用 FOR 命令时，指定变量请使用 %%variable
而不要用 %variable。变量名称是区分大小写的，所以 %i 不同于 %I.

如果启用命令扩展，则会支持下列 FOR 命令的其他格式:

FOR /D %variable IN (set) DO command [command-parameters]

    如果集中包含通配符，则指定与目录名匹配，而不与文件名匹配。

FOR /R [[drive:]path] %variable IN (set) DO command [command-parameters]

    检查以 [drive:]path 为根的目录树，指向每个目录中的 FOR 语句。
    如果在 /R 后没有指定目录规范，则使用当前目录。如果集仅为一个单点(.)字符，
    则枚举该目录树。

FOR /L %variable IN (start,step,end) DO command [command-parameters]
该集表示以增量形式从开始到结束的一个数字序列。因此，(1,1,5)将产生序列
    1 2 3 4 5，(5,-1,1)将产生序列(5 4 3 2 1)

FOR /F ["options"] %variable IN (file-set) DO command [command-parameters]
FOR /F ["options"] %variable IN ("string") DO command [command-parameters]
FOR /F ["options"] %variable IN ('command') DO command [command-parameters]

    或者，如果有 usebackq 选项:

FOR /F ["options"] %variable IN (file-set) DO command [command-parameters]
FOR /F ["options"] %variable IN ("string") DO command [command-parameters]
FOR /F ["options"] %variable IN ('command') DO command [command-parameters]

    fileset 为一个或多个文件名。继续到 fileset 中的下一个文件之前，
    每份文件都被打开、读取并经过处理。处理包括读取文件，将其分成一行行的文字，
    然后将每行解析成零或更多的符号。然后用已找到的符号字符串变量值调用 For 循环。
    以默认方式，/F 通过每个文件的每一行中分开的第一个空白符号。跳过空白行。
    你可通过指定可选 "options" 参数替代默认解析操作。这个带引号的字符串包括一个
    或多个指定不同解析选项的关键字。这些关键字为:

        eol=c           - 指一个行注释字符的结尾(就一个)
        skip=n          - 指在文件开始时忽略的行数。
        delims=xxx      - 指分隔符集。这个替换了空格和制表符的
                          默认分隔符集。
        tokens=x,y,m-n  - 指每行的哪一个符号被传递到每个迭代
                          的 for 本身。这会导致额外变量名称的分配。m-n
                          格式为一个范围。通过 nth 符号指定 mth。如果
                          符号字符串中的最后一个字符星号，
                          那么额外的变量将在最后一个符号解析之后
```
1. 查看当前文件下的目录文件
> for /d %i in (*) do echo %i
2. 从指定文件中以空格分开，第二列后打开输出每行内容
> for /F "token=2-3,delims=" %i in (e:\1.txt) do echo %i