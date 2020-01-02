---
 title: Vscode配置python环境
 date: 2019-12-28 10:16:19
 tags: Vscode
 categories:
    - 配置
    - 编程
---

 ## Vscode简介
   VSCode(Visual Studio Code):它是一个免费的，开源的跨平台编辑器。之所以强调‘编辑器’，可能是VSCode并无意成为一个全尺寸的集成开发环境，也就是IDE。它是由Erich Gamma把Monaco Editor移植到桌面平台上，成为了现今的VSCode. [下载地址](https://code.visualstudio.com/Download)

 ## 配置python设置
 1. 下载python,[下载地址](https://www.python.org/downloads/),添加python进入系统环境变量
![5792949-31833c97ae3bd272.gif](https://i.loli.net/2019/12/26/m9sKiH2fx8kcdlz.gif)
 2. 下载python集合插件
   - 打开VScode，按下快捷键Ctrl+Shift+X，进入插件管理页面。
   - 在搜索栏输入python。
   - 选择插件，点击安装
![5792949-8a2465cfedc2530a.gif](https://i.loli.net/2019/12/26/aN85DACXt9hiQTn.gif)
 3. 设置配置文件
   文件-首选项-设置 search pythonpath,将python安装目录填写进去，左下角可以点击切换python版本和环境
 4. vscode语言包安装与选择
   > 使用快捷键组合【Ctrl+Shift+p】，在搜索框中输入“configure display language”，点击安装zh语言包确定后;重启vscode工具.
<!--more-->
 
 ## 常用python插件推荐
 
 1. 图标插件
    > Vscode-icons VSCode 文件图标
    此插件可以帮助我们根据不同的文件类型生成对应的图标，这样我们在侧边栏查看文件列表的时候直接通过图标就可以区分文件类型。
    ![Image.jpg](https://i.loli.net/2019/12/26/BkKfTltDEqHdoUF.jpg)
 2. 单词拼写插件
    > Code Spell Checker 单词拼写检查
    我们在编写代码的时候经常会不小心拼写错误造成软件运行失败，安装这个插件后会自动帮我们识别单词拼写错误并且给出修改建议，大大帮我们减轻了排除bug的压力
    ![Image.gif](https://i.loli.net/2019/12/26/sDfNOU2SojZc1kp.gif)
 3. 格式化代码插件
      ##### yapf格式化库
      - 首先安装yapf库
         ```
         pip install yapf
         ```
      - 然后在VS Code菜单栏中依次打开 File—Preferences—Settings，搜索框中搜索“python.formatting.provider”，然后在下拉菜单中选择“yapf”即可
      - 使用时，只需要选中需要格式化的Python代码，然后右键选择“Format Selection”即可实现自动格式化
 4. 高亮插件
      ##### 括号高亮插件Bracket Pair Colorizer
      这个插件的作用是给代码中的括号增加颜色，同一对括号是相同的颜色，尤其是在括号中还包着括号的时候，看起来更加的清晰

    
 ## Vscode常用快捷键
 
 - 常用的一些快捷鍵
   ![Image.png](https://i.loli.net/2019/12/26/c1NnhtgoupvTfqM.png)
 
 - 自定义快捷键的设置
   > 文件-首选项-键盘快捷方式


 ## 其他操作
 
 1. 设置文件头模板
   - 在VS code中创建Snppets: 首先在File(文件)>Preferences(首选项)>Snippets(用户代码片段)，选择markdown, 在新增加的markdown.json文件中增加如下内容
   ```
      {
	"markdown title":{
		"prefix": "mdtit",
		"body": [
			"---",
			" title: $TM_FILENAME_BASE",
			" date: $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND",
			" tags:",
			" categories:",
			"---"
		],
		"description": "notes title"
	}
   }
   ```
   - 这样子，就能调用ctrl+shift+p,然后insert Snippt，选择mdtitle,就能插入自动插入一段声明，然后加入日期，标签，分类等信息
   python文件头配置如下:
   ```
   "Print to console": {
		"prefix": "py",
		"body": [
			"# -*- coding: utf-8 -*-",
			"'''",
			"# @CreatedTime :  $CURRENT_YEAR_SHORT年$CURRENT_MONTH_NAME_SHORT$CURRENT_DATE日 $CURRENT_HOUR:$CURRENT_MINUTE",
			"# @filename : $TM_FILENAME",
			"# @author: bb",
			"'''",
		],
	}
   ```

 2. 好用的便捷命令
    - 打开文件目录
    ```
    \\ 用Vscode打开当前项目目录文件(新建窗口)
    code .

    \\ 打开新的项目目录(正在打开的会强制关闭)
    code -r .
    ```
    - 代码格式化检查
    - 在项目中查找关键字
    - 整行代码上下移动
    > Alt + 上下
   - 快速打开文件
      > Cmd+P （win Ctrl+P）输入你要打开的文件名,回车打开
   
   - 插件搜索框
      > vscode插件搜索框（ctrl + shift + x）
   
   - 命令操作界面
      > vscode命令界面（ctrl + shift + P）
   
   - 跳转到指定行
      > Ctrl + g 输入行号回车跳转
 
 3. 代码审计函数跳转
   > 选择函数，按住Ctrl左键跳转

 
