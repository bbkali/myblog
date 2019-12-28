---
title: xlwt模块使用总结
date: 2019-03-14 09:12:17
tags:
    - python
    - xlrt
    - excle
    - csv
categories:
    - 笔记总结
    - python模块
---
## 安装模块
```bash
pip install xlwt
```
- 手动下载安装移步[官网](http://pypi.python.org/pypi/xlwt)
- [文档](https://xlwt.readthedocs.io/en/latest/)

## 快速上手
```python
import xlwt

# 创建一个excle文件
file = xlwt.Workbook(encoding='ascii') #注意这里的Workbook首字母是大写

# 新建一个sheet
table = file.add_sheet('sheet name')
"""
如果对一个单元格重复操作
会引发Exception: Attempt to overwrite cell
所以在打开时加cell_overwrite_ok=True 解决
"""
table = file.add_sheet('sheet name',cell_overwrite_ok=True )

# 写入数据table.write(行,列,value)
table.write(0,0,'test')
file.save('demo.xls')  # 保存文件

```
<!--more-->
## 行高列宽
```python
table.row(0).height_mismatch = True
table.row(0).height = 800  # 设置行高

table.col(0).width = 256 * 12 # 设置列宽

```

## 风格样式
|类|作用|
|:----:|:----:|
|Font()|用来设置单元格字体内容，如字体类型、大小等|
|borders()|设置单元格边框线粗细|
|partern()设置单元格背景颜色|
|XFStyle()|设置风格类型|
|alignment()|用来设置了字体水平居中、垂直居中、自动换行|
设置单元格风格，写成了一个函数,如下:
```python
# 参数为颜色。可自定义不同设置参数如是否粗体、颜色、字体大小、字体边框来控制样式
def body_style(pattern=None): 

    # 设置excel单元格风格
    font = xlwt.Font()  # Create Font

    font.name = "SimSun"  # 宋体
    font.height = 20 * 12  # 字体大小
    font.colour_index = 1 # 设置字体颜色 0 - 128 可填
    font.bold = True # 设置粗体
    style = xlwt.XFStyle()  # 初始化创建风格类
    style.alignment.horz = 2  # 字体居中
    style.alignment.vert = 1
    style.alignment.wrap = 1
    if pattern:
        pat = xlwt.Pattern() # 初始化背景类
        # 设置底纹的图案索引，1为实心，2为50%灰色，对应单元格格式中填充中的图案样式
        pat.pattern = 1  

        # 设置背景颜色,文件单元格格式中填充中的背景色
        # 可填0-128种颜色，0黑色，1白色，2红色，3绿色，4蓝色，5黄色等
        pat.pattern_fore_colour = pattern 

        # 设置底纹的背景色，对应为excel文件单元格格式中填充中的图案颜色
        # 可填0-128种颜色，0黑色，1白色，2红色，3绿色，4蓝色，5黄色等
        patterni.pattern_back_colour = 35

        # 为设置好的font、borders赋值style
        style.pattern = pat
    borders = xlwt.Borders() # 初始化创建边框类
    borders.left = 1 # 左边框大小
    borders.right = 1 # 右边框大小
    borders.top = 1 # 上边框大小
    borders.bottom = 1 # 下边框大小

    # 为设置好的font、borders赋值style
    style.font = font
    style.borders = borders
    return style # 返回整个样式类
```

## 写入数据
```python
# 单个单元格写入内容
table.write(m_row, m_col, u'内容', body_style())

# 合并单元格，前四个参数分别是起止的行列位置
table.write_merge(1, 2, m_col, m_col+3, u'合并的内容', body_style())
```

## 案例书写
![案例](https://s2.ax1x.com/2019/03/14/AArn3j.png)
```python
import xlwt
def body_style(patterns=None,bold=False,st=1): 

    # 设置excel单元格风格
    font = xlwt.Font()  # Create Font

    font.name = "SimSun"  # 宋体
    font.height = 20 * 28  # 字体大小
    font.colour_index = 0 # 设置字体颜色 0 - 128 可填
    font.bold = bold # 设置粗体
    style = xlwt.XFStyle()  # 初始化创建风格类
    style.alignment.horz = 2  # 字体居中
    style.alignment.vert = 1
    style.alignment.wrap = 1
    if patterns:
        pat = xlwt.Pattern() # 初始化背景类
        # 设置底纹的图案索引，1为实心，2为50%灰色，对应单元格格式中填充中的图案样式
        pat.pattern = st  

        # 设置背景颜色,文件单元格格式中填充中的背景色
        # 可填0-128种颜色，0黑色，1白色，2红色，3绿色，4蓝色，5黄色等
        #pat.pattern_fore_colour = patterns

        # 设置底纹的背景色，对应为excel文件单元格格式中填充中的图案颜色
        # 可填0-128种颜色，0黑色，1白色，2红色，3绿色，4蓝色，5黄色等
        #pat.pattern_back_colour = 35

        # 为设置好的font、borders赋值style
        style.pattern = pat
    borders = xlwt.Borders() # 初始化创建边框类
    borders.left = 1 # 左边框大小
    borders.right = 1 # 右边框大小
    borders.top = 1 # 上边框大小
    borders.bottom = 1 # 下边框大小

    # 为设置好的font、borders赋值style
    style.font = font
    style.borders = borders
    return style # 返回整个样式类
workbook = xlwt.Workbook(encoding='utf-8')
table = workbook.add_sheet('sheet1',cell_overwrite_ok=True)
header_cls=['python','java','c++']
header_row=['语言','使用人数','使用率','涉及行业','备注']
title='当前热门语言统计'


# 书写标题
table.write_merge(0,0,0,4,title,body_style(patterns=1,bold=True,st=2))
# 设置标题
for i in range(len(header_row)):
    table.row(i).height = 80
    table.col(i).width = 256 * 25
    table.write(1,i,header_row[i],body_style(bold=False,st=1))
# 设置第一列
for i in range(len(header_cls)):
    table.write(2+i,0,header_cls[i],body_style(bold=False,st=1))
workbook.save('测试.xls')

```

