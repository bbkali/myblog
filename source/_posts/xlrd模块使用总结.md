---
title: xlrd模块使用总结
date: 2019-03-12 16:13:16
tags:
    - python
    - xlrd
    - excle
    - csv
categories:
    - 笔记总结
    - python模块
---

## 安装模块
```bash
pip install xlrd
```
- 手动下载安装移步[官网](http://pypi.python.org/pypi/xlrd)

## 常用方法
```python
x1=xlrd.open_workbook(filename)
# 打开中文文件时需要转码
filename = filename.decode('utf-8')
```
以test.xlsx为例有first、second、thirt三个sheet:

|(1,1)|(1,2)|第三列|
|:----:|:----:|:----:|
|(2,1)| 0.2323123|[1,2]|
|TRUE| null |\#NAME?|
<!--more-->
### 获取sheet:
- 获取所有sheet名字：x1.sheet_names（）
- 获取sheet数量：x1.nsheets
- 获取所有sheet对象：x1.sheets()
- 通过sheet名查找first表：x1.sheet_by_name("first”)
- 通过索引查找第一个sheet：x1.sheet_by_index(0)

```bash
sheet_names: ['first', 'second', 'third']
sheet_number: 3
sheet_object: [<xlrd.sheet.Sheet object at 0x000002338F6146A0>, <xlrd.sheet.Sheet object at 0x000002338F6146D8>, <xlrd.sheet.Sheet object at 0x000002338F614710>]
By_name: <xlrd.sheet.Sheet object at 0x000002338F6146A0>
By_index: <xlrd.sheet.Sheet object at 0x000002338F6146A0>
```

## 获取表内容
```
sheet1=x1.sheet_by_name('first')
sheet1=x1.sheet_by_index(0)
```
- 获取表名称 sheet1.name
- 获取表行数 sheet1.nrows
- 获取表列数 sheet1.ncols

### 行操作
- 获取第一行所有内容，合并单元格，首行显示值，其它为空。 sheet1.row_values(0)  
- 获取单元格值类型和内容 sheet1.row(0)         
- 获取单元格数据类型 sheet1.row_types(0)   
```python
sheet1=x1.sheet_by_name("first")
for i in range(sheet1.nrows):
    print(sheet1.row_values(i))

['(1,1)', '(1,2)', '第三列']
['(2,1)', 0.2323123, '[1,2]']
[1, 'null', 29]
```

### 列操作
- 取第1行，第6~10列（不含第10表）sheet1.row_values(0, 6, 10)   
- 取第1列，第0~5行（不含第5行）sheet1.col_values(0, 0, 5)    
- 获取单元格值类型和内容，同sheet1.row(0) sheet1.row_slice(2, 0, 2)     
- 获取单元格数据类型 sheet1.row_types(1, 0, 2)     

### 特定单元格获取
- 获取单元格值：
```python
sheet1.cell_value(1, 2) # 第二行第三列的值
sheet1.cell(1, 2).value
sheet1.row(1)[2].value 
```
- 获取单元格类型：
```python
sheet1.cell(1, 2).ctype
sheet1.cell_type(1, 2)
sheet1.row(1)[2].ctype
```

## 其他表示
- (0,0)转换成A1表示 xlrd.cellname(0, 0)   
- (0,0)转换成$A$1 xlrd.cellnameabs(0, 0) 
- 把列由数字转换为字母表示 xlrd.colname(30)

## 数据类型
|类型|表示方式|
|:----:|:----:|
|空|0|
|字符串|1|
|数字|2|
|日期|3|
|布尔|4|
|error|5|

## 常用函数
1. 读取excle文件的数据
```python
import xlrd
def read_value(fileio,name):
    """
    fileio:xlrd传入的文件
    sheetname:为sheet表
    """
    sheet1=x1.sheet_by_name(name)
    for i in range(sheet1.nrows):
        for c in range(sheet.ncols):
            print(sheet1.row_values(i)[c])

def read_excle(filename,sheetname='all'):
    """
    filename:为打开的excle文件
    sheetname:为sheet表
    """
    x1=xlrd.open_workbook(filename)
    if sheetname =='all':
        for name in x1.sheet_names:
            read_value(x1,name)
    else:
        read_value(x1,sheetname)

```
