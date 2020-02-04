---
 title: powershell语法总结
 date: 2020-01-27 20:13:00
 tags: powershell
 categories: powershell
---

### 认识PowerShell
> $psversiontable查看版本
### PowerShell强大之处
1. 快捷方便
2. 面向对象
3. 支持兼容.net vbs等
4. 可拓展性
### PowerShell快捷键
```
alt+f7 清除命令的历史纪录
esc 清空当前命令行
```
<!--more-->
### PowerShell执行外部命令
> &"notepad"
### PowerShell命令集
以`动名词`来命名命令
* cmd 命令在powershell中可以直接使用
> get-command # 获取所有命令列表

* 帮助命令走天下
> get-help
### PowerShell别名使用
> get-alias -name ls # 查询ls的原始命令
### PowerShell自定义别名
> set-alias -name pad -value notepad # 设置临时别名，将notepad赋给pad别名
> del alias:pad # 删除别名
> export-alias 1.psl # 导出别名
> import-alias -force 1.psl # 强制导入别名
### PowerShell变量基础
* 等于号赋值
> $name="xx"
* 特殊变量名称用花括号包围
> ${"asdsad asdsd" var ()}
### PowerShell变量操作
* 支持多变量赋值
$name,$name2=1,2
* 查看正在使用的变量
> Get-Variable num* # 查找num特定变量值
* 确定变量是否存在
> test-path variable:num1 # 返回的是布尔值
* 删除变量名
> Remove-Variable num1 

### PowerShell自动化变量
**常用的变量**
* $pid
* $home

### PowerShell环境变量
> ls env: # 查看当前环境变量

> $env:os # 输出某个键的值

> $env:0s="Linux" # 临时赋值变量

* 设置永久环境变量（.net方式）
> [environment]\::setenvironmentvariable("PATH","D:\","User")
### PowerShell脚本执行策略
> get-executionpolicy # 查看当前运行策略

```
策略分类
> get-help set-executionpolicy

语法
    Set-ExecutionPolicy [-ExecutionPolicy] {Unrestricted | RemoteSigned | AllSigned | Restricted | Default | Bypass | U
    ndefined} [[-Scope] {Process | CurrentUser | LocalMachine | UserPolicy | MachinePolicy}] [-Confirm] [-Force] [-What
    If] [<CommonParameters>]

```

> set-executionpolicy RemoteSigned # 设置可以运行的策略

### PowerShell与其他脚本程序的互相调用
1. powershell直接可以执行bat、vbs、psl文件
2. bat调用powershell 需要使用 powershell &"文件路径"
### PowerShell条件操作符
* -eq  #等于
* -lt #小于
* -gt #大于
* -contains #不包含
> 1,3,5 -contains 3
* -notcontains
* -not
* -and
* -or
* -ne #不等于
> 1,3,5 -ne 3
### PowerShell条件判断【if语句】
> if($num -gt 100){"1"} elseif($num -eq 100){"0"} else {"-1"}
### PowerShell条件判断【switch语句】
```
$number = 49
switch($number)
{
    {$_ -le 50} {"此数值小于50"}
    {$_ -eq 50} {"此数值等于50"}
    {$_ -gt 50} {"此数值大于50"}
}
\\ $_代表变量
```
### PowerShell循环结构【foreach语句】
```
$arr = 1,2,3,4,5 或者 $arr=1..10
foreach ($n in $arr)
{
    if($n -gt 5)
        {
            $n
        }
}
```
### PowerShell循环结构【while语句】
```
$num = 15
while($num -gt 15)
{
    $num
    $num=$num-1
}
```
* dowhile 至少运行一次
```
do
{
    $num
    $num=$num-1
}
while($num -gt 15)
```
### break和continue关键字使用
**break跳出**
```
$num=1
while($num -lt 6)
{
    if($num -gt 4)
    {
        break
    }
    else
    {
        $num
        $num++
    }
}
```
**continue跳过**
```
$num=1
while($num -lt 6)
{
    if($num -gt 4)
    {
        break
    }
    else
    {
        $num
        $num++
    }
}
```
### PowerShell循环结构【for语句】
```
$sum=0
for($i=1;$i -;e 100;$i++)
{
    $sum=$sum+$i
}
$sum
\\ 计算前100个数字的和
```
### PowerShell循环结构【switch循环】
```
$num=1..10
swith($num)
{
    {($_ % 2) -eq 0} {"$_ 是偶数"}
    {($_ % 2) -ne 0} {"$_ 是奇数"}
}
```
### PowerShell数组的创建
```
$arr = ipconfig
$arr
$arr -is [array]
\\ 判断是否是数组
```
> $arr=ipconfig #cmd命令也可以执行
> $arr=@() #创建空数组
> $arr=1..10,"string",(get-date)#创建混合数组 

### PowerShell访问数组
> $arr[0..2]
### PowerShell自定义函数及调用
```
function myping($url)
{
    ping $url
}
myping www.baidu.com
```
### PowerShell函数返回值
**return**
### PowerShell定义文本
> `转义
### PowerShell实现用户交互
```
$input=read-host "请输入具体的路径"
"您当前的路径是:$input"
```
### PowerShell格式化字符串
```
"my name is {0} ,iam {1} years old" -f $name,$age
```
