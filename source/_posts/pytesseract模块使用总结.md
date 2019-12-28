---
title: pytesseract模块使用总结
date: 2019-03-12 16:13:35
tags:
    - python
    - pytesseract
categories:
    - 笔记总结
    - python模块
---
## 安装环境
1. Python-tesseract需要python 2.6+或python 3.x.
2. 需要Python Imaging Library（PIL）（或Pillow fork）。在Debian / Ubuntu下，这是包python-imaging或python3-imaging。
3. 安装 [Tesseract OCR](https://github.com/tesseract-ocr/tesseract) （有关如何在Linux，Mac OSX和Windows上安装引擎的其他信息）。你必须能够调用的Tesseract命令正方体。如果不是这种情况，例如因为tesseract不在您的PATH中，则必须更改“tesseract_cmd”变量pytesseract.pytesseract.tesseract_cmd。在Debian / Ubuntu下，您可以使用包tesseract-ocr.[pytesseract文档](https://pypi.org/project/pytesseract/)
<!--more-->

## 快速开始
```python

from PIL import Image
import pytesseract

pytesseract.pytesseract.tesseract_cmd = r'<full_path_to_your_tesseract_executable>'

print(pytesseract.image_to_string(Image.open('test.png')))

print(pytesseract.image_to_string(Image.open('test-european.jpg'), lang='fra'))

print(pytesseract.image_to_boxes(Image.open('test.png')))

print(pytesseract.image_to_data(Image.open('test.png')))

print(pytesseract.image_to_osd(Image.open('test.png')))

print(pytesseract.image_to_string('test.png'))

pdf = pytesseract.image_to_pdf_or_hocr('test.png', extension='pdf')

hocr = pytesseract.image_to_pdf_or_hocr('test.png', extension='hocr')
```

## 功能说明
|函数|功能|
|:----:|:----:|
|get_tesseract_version|返回系统中安装的Tesseract版本|
|image_to_string|将图像上的Tesseract OCR运行结果返回到字符串|
|image_to_boxes|返回包含已识别字符及其框边界的结果|
|image_to_data|返回包含框边界，置信度和其他信息的结果。需要Tesseract 3.05+|
|image_to_osd|返回包含有关方向和脚本检测的信息的结果|

## 参数说明
```bash
image_to_data（image，lang = None，config =''， nice = 0，output_type = Output.STRING）
```
- image Object，PIL Image /由Tesseract处理的图像的NumPy数组
- lang String，Tesseract语言代码字符串
- config String，作为字符串的任何其他配置，例如：config =' - psm 6'
- nice Integer，修改Tesseract运行的处理器优先级。Windows不支持。Nice调整了unix-like进程的优点。
- output_type Class属性，指定输出的类型，默认为string。有关所有支持类型的完整列表，请检查pytesseract.Output类的定义

## 常用用法
```python
from PIL import Image
code = pytesseract.image_to_string(
            Image.open('test.png'), lang='chi_sim', config='--psm 6').replace(' ', '')
```
## 参考资料
1. https://www.cnblogs.com/hupeng1234/p/7136442.html
2. http://python.jobbole.com/84625/
3. http://www.cnblogs.com/kappaboy/p/5910226.html
