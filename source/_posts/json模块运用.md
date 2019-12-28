---
title: json模块运用
date: 2019-02-25 21:53:52
tags: 
    - python
    - json
categories:
    - 学习资料
    - python模块
---

## json简介及运用场景
- JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式,son   模块提供了四个方法： `dumps`、`dump`、`loads`、`load`

## json常用函数及运用
|函数|描述|
|:----:|:----:|
|json.dumps或者json.dump|将 Python 对象编码成 JSON 字符串|
|json.loads或者json.load|将已编码的 JSON 字符串解码为 Python 对象|

### `json.dumps`
```python
def dumps(obj, skipkeys=False, ensure_ascii=True, check_circular=True,
        allow_nan=True, cls=None, indent=None, separators=None,
        default=None, sort_keys=False, **kw):

```
1. Serialize ``obj`` to a JSON formatted ``str``.
2. 序列号 “obj” 数据类型 转换为 JSON格式的字符串
<!--more-->

### `json.loads`
```python
def loads(s, encoding=None, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw):
    """Deserialize ``s`` (a ``str`` instance containing a JSON document) to a Python object.
       将包含str类型的JSON文档反序列化为一个python对象"""
```

### `json.dump`
```python
def dump(obj, fp, skipkeys=False, ensure_ascii=True, check_circular=True,
        allow_nan=True, cls=None, indent=None, separators=None,
        default=None, sort_keys=False, **kw):
```
1. `skipkeys`为真（默认：False），那么字典键是一个基本类型（不str，int，float，bool， None）将被跳过，而不是养TypeError。
2. `ensure_ascii`为true（默认值），则保证输出转义所有传入的非ASCII字符。如果ensure_ascii为false，则这些字符将按原样输出。
3. 如果`check_circular`为false（默认值:)True，则将跳过对容器类型的循环引用检查，并且循环引用将导致OverflowError（或更糟）。
4. 如果`allow_nan`是假的（默认：True），那么这将是一个ValueError序列化超出范围的float值（nan，inf，-inf在JSON规范的严格遵守）。如果allow_nan是真实的，其JavaScript当量（NaN， Infinity，-Infinity）将被使用。
5. 如果`indent`是一个非负整数或字符串，那么JSON数组元素和对象成员将使用该缩进级别进行漂亮打印。缩进级别为0，为负，或""仅插入换行符。 None（默认值）选择最紧凑的表示。使用正整数缩进缩进每个级别的许多空格。如果indent是一个字符串（例如"\t"），则该字符串用于缩进每个级别。
6. 如果`sort_keys`为true（默认值:)False，则字典的输出将按键排序

### `json.load`
```python
def load(fp, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw):
```

1. `object_hook`是一个可选函数，将使用解码的任何对象文字的结果调用（adict）。将使用object_hook的返回值 而不是dict。此功能可用于实现自定义解码器（例如JSON-RPC 类提示）。
2. `object_pairs_hook`是一个可选函数，将使用有序的对列表对解码的任何对象文字的结果进行调用。将使用object_pairs_hook的返回值而不是dict。此功能可用于实现依赖于键和值对被解码的顺序的自定义解码器（例如， collections.OrderedDict()将记住插入的顺序）。如果还定义了object_hook，则object_pairs_hook优先。
3. 如果指定了`parse_float`，将使用要解码的每个JSON float的字符串调用。默认情况下，这相当于float(num_str)。这可用于为JSON浮点数使用另一种数据类型或解析器（例如decimal.Decimal）。
4. 如果指定了`parse_int`,将使用要解码的每个JSONint的字符串调用。默认情况下，这相当于int(num_str)。这可以用于为JSON整数使用另一种数据类型或解析器（例如float。
5. parse_constant：如果指定，将与下列字符串之一叫'-Infinity'，'Infinity'，'NaN'。如果遇到无效的JSON号，这可用于引发异常

## json类型转换表
|JSON|Python|python-->Json|
|:----:|:----:|:----:|
|object|dict|json.dumps({'1':'测试'})|
|array|list|json.dumps([1,2,3,4,5])|
|string|unicode|json.dumps('测试')|
|number(int)|int,long|json.dumps(123)|
|number(real)|float|json.dumps(3.141592653)|
|true|True|json.dumps(True)|
|false|False|json.dumps(False)|
|null|None|json.dumps(None)|

## 参考文档
[官方文档](https://docs.python.org/3.6/library/json.html)

