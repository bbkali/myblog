---
title: Sublime_text3配置python编写环境
date: 2019-02-28 17:44:36
tags:
    - python
    - Sublime
categories:
    - 笔记总结
    - Sublime
---

## 环境需要

- [python下载](https://www.python.org/downloads/)

- [Sublime_text3下载](http://www.sublimetext.com/3)

- sublime注册码如下，如果不可用请百度：

  ```
  —– BEGIN LICENSE —–
  Michael Barnes
  Single User License
  EA7E-821385
  8A353C41 872A0D5C DF9B2950 AFF6F667
  C458EA6D 8EA3C286 98D1D650 131A97AB
  AA919AEC EF20E143 B361B1E7 4C8B7F04
  B085E65E 2F5F5360 8489D422 FB8FC1AA
  93F6323C FD7F7544 3F39C318 D95E6480
  FCCC7561 8A4A1741 68FA4223 ADCEDE07
  200C25BE DBBC4855 C4CFB774 C5EC138C
  0FEC1CEF D9DCECEC D3A5DAD1 01316C36
  —— END LICENSE ——
  ```

  

## 下载插件

- 安装Package Control管理插件
  1.  使用ctrl + ` (感叹后左边的那个键位)，输入以下内容
  2.  安装好后,在Preferences下出现Package Control
  3.  sublime_text3用如下安装代码:
<!--more-->

```python
import urllib.request,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

sublime_text2如下安装代码：

```python
import urllib2,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation'
```

手动下载[package](https://packagecontrol.io/Package%20Control.sublime-package)

- 配置python3环境
  1. 打开 Tools > Build System > New Build System

输入以下内容,输入你自己的python安装目录位置：

```bash
{
    "encoding": "utf-8",  
    "working_dir": "$file_path",  
    "shell_cmd": "G:\\软件\\python\\python.exe -u \"$file\"",  
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",  
    "selector": "source.python"
}
```

- 中文汉化插件localiza

按ctrl+shift+p打开Package Control管理插件，输入localiza安装，安装好后在菜单栏-->帮助-->切换语言

- 安装Anaconda插件

  1. 代码提示等许多功能，必备.由于Anaconda插件本身无法知道Python安装的路径，所以需要设置Python主程序的实际位置。选择Settings-Default选项，搜寻“python_interpreter” key，并键入python主程序的磁盘位置

  ```bash
  "python_interpreter": "G:\\软件\\python\\python.exe",
  ```

  2. 选择Preferences->Package Settings->Anaconda->Settings User，添加如下内容，键入以下json数据。保存，重启ST3即可

  ```bash
  {
      "python_interpreter":"G:\\软件\\python\\python.exe",
      "suppress_word_completions":true,
      "suppress_explicit_completions":true,
      "comlete_parameters":true,
      "swallow_startup_errors":true,
      "anaconda_linting":false
  }
  ```

- 安装SublimeTmpl插件

  该模块，可以通过快捷键按照模板快速新建文件，安装SublimeTmpl插件后，打开Preferences->Package Settings->SublimeTmpl->Settings User，添加以下自定义内容,可在/templates目录下找文件模板，并修改

  ```bash
  {  
      "disable_keymap_actions": false, // "all"; "html,css"  
      "date_format" : "%Y-%m-%d %H:%M:%S",  
      "attr": {  
          "author": "yourname",  
          "email": "youremail",  
          "link": "yourlink"  
      }  
  }
  ```

  python模板文件为python.tmpl,以我修改的如下

  ```bash
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-
  # @Date    : ${date}
  # @Author  : ${author}
  # @Version : ${Id}
  ```

  打开Preferences->Key Bindings，可自定义打开模板快捷键。

- 安装sublimeREPL

  添加快捷键后，可直接运行当前文件，非常方便,安装SublimeREPL插件后，打开Preferences->Key Bindings，添加快速运行快捷键如下:

  ```bash
  [
      {
          "keys": ["f5"],
          "caption": "SublimeREPL: Python - RUN current file",
          "command": "run_existing_window_command",
          "args": {
              "id": "repl_python_run",
              "file": "config/Python/Main.sublime-menu"
          }
      }
  ]
  ```

- 安装Emmet插件

  编写前端代码必备，能快速实现变现html/css

- 安装sublimeLinter插件

  高亮提示代码错误

- 安装autopep8插件

  自动格式化代码符合pep8标准，设置默认格式化快捷键,Preferences->setting-user

  ```bash
  [
    { "keys": ["ctrl+8"], "command": "auto_pep8", "args": {"preview": true} },
    { "keys": ["ctrl+shift+8"], "command": "auto_pep8", "args": {"preview": false} }
  ]
  ```

- 安装terminial插件

  可以快捷键调出命令行，配置cmd路径如下,ctrl+shift+t的调用cmd

  ```bash
  {
      "terminal": "C:\\windows\\system32\\cmd.exe",
       "parameters": ["/START","%CWD%"]
  }
  ```

  