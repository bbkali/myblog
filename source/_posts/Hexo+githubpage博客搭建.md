---
title: Hexo+Githubpage博客搭建
date: 2019-01-26 22:04:01
tags: 
- hexo 
- Github
categories: 
- 笔记总结
- hexo
---
## 搭建实践

### 环境准备
1. 安装`node.js`
[下载地址](https://nodejs.org/en/),直接双击下载下来的msi文件，一路下一步就ok了，安装完之后调出命令行或者node命令输入终端，输入node -v看到版本信息就表明安装成功了。如果自定义安装路径，则需要修改环境变量中Path的对应路径。
2. 安装 `git`
[下载地址](https://git-scm.com/),直接双击下载下来的git安装程序，同node安装，安装完之后右键发现多了两个Git GUI Here 、Git Bash Here，点击后者，然后输入git –version出现版本信息就表明安装成功了

### git连接Github配置
1. 创建新的repository(仓库)
- 名称必须要遵守格式：账户名.Github.io。选择public，并且需要勾选Initialize this repository with a README。创建项目成功后，便可通过https://账户名.Github.io以网页的形式访问所创建的项目。
2. 设置用户名及邮箱：
```bash
$ git config --global user.name "username"
$ git config --global user.email "xxx@qq.com"
```
<!--more-->
3. 配置SSH key
检查本机是否存在的ssh密钥
```
$ cd ~/.ssh
```
- 如果提示：No such file or directory 说明你是第一次使用git，使用下述命令生成 ssh key
```bash
$ ssh-keygen -t rsa -C "邮件地址"
```
- 然后连续3次回车，最终会生成一个文件在用户目录下，打开用户目录，找到.ssh\id_rsa.pub文件，记事本打开并复制里面的内容，打开你的Github主页，进入个人设置 -> SSH and GPG keys -> New SSH key，将刚复制的内容粘贴到key那里，title随便填，保存。
4. 测试是否成功
```bash
$ ssh -T git@Github.com
```
- 如果提示Are you sure you want to continue connecting (yes/no)?，输入yes

### 搭建个人博客

1. 安装hexo
- 安装淘宝源的cnpm ,在git bash中输入下面整段
```bash
$ alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
```
2. 安装hexo
```bash
$ cnpm install -g hexo
```
3. 安装个人博客
- 进入到本地的博客存放路径，例如f:\blog
```bash
$ cd f:\\blog
```
- 初始化
```
$ hexo init
```
4.选择Fan主题，你还可以从[hexo themes](https://hexo.io/themes/)上选择你喜欢的主题下载
```bash
$ git clone https://github.com/fan-lv/Fan themes/Fan
```
- 接着，打开hexo\blog\themes\vexo\_config.yml，将用户信息修改为自己的信息
5. 部署到Github上
- 打开hexo\blog\_config.yml，修改主题为vexo，并在最后添加如下内容。
```bash
deploy:
  type: git
  repository: git@github.com:bolerolily/bolerolily.github.io.git
  branch: master
```
- 其中，repository所填写的内容可通过如下图片所呈现的方式获取，记得选择”Use SSH”
6. 安装扩展
```bash
$ cnpm install hexo-deployer-git --save
```
7. 查看并提交到Github上
```bash
$ hexo s
$ hexo -g d
```

## 问题总结

1. 缩略图显示图片
```
<!--more-->
```
2. 代码高亮的
3. 标签页和分类页面效果图展示
- 创建标签页面
```
hexo new page tags
```
- 基本设置，在`source/tags/index.md`中添加如下配置
```bash
type: "tags"
layout: "tags"
```
- 创建分类页面
```
hexo new page categories
```
- 基本设置，在`source/categories/index.md`中添加如下配置
```bash
type: "categories"
layout: "categories"
```
4. 搜索功能
- 到[Algolia官网](到Algolia官网，注册一个账号，当然你可以用Github账号)，注册一个账号，当然你可以用Github账号和Google账号
- 新建一个新的Index，当然用已有的也可以，再在新的Index上进行基础设置
- 打开API Keys页面，里面的信息一会儿要用到
- 安装algolia插件
```bash
$ cnpm install hexo-algolia --save
```
- 在Hexo工程根目录的_config.yml中加入如下配置，注意改成前面API Keys页面相应配置
```
algolia: 
applicationID: 'your applicationID' 
apiKey: 'your apiKey' 
adminApiKey: 'your adminApiKey' 
indexName: 'your indexName' 
chunkSize: 5000
```
- 在博客根目录下执行
```bash
export(windows 为 set) (Powershell 用 $env:) HEXO_ALGOLIA_INDEXING_KEY=你的Search-Only API key
 set (Mac和git bash 为 export) (Powershell 用 $env:) HEXO_ALGOLIA_INDEXING_KEY     #查看是否设置成功如果没有值就设置失败
```
- 执行下面语句，必要时先进行hexo clean，确保最后得到提交成功提示
```bash
$ hexo algolia
```
5. 序号样式
> "."后要有一个空格，与图片行不要空行
6. 标签和分类的生成
- 在 Front-matter中的分类标记后面写下多个分类,[官方文档](https://hexo.io/zh-cn/docs/front-matter.html)
```
categories:
- Diary
tags:
- PS3
- Games
```
- 在`scaffolds/post.md`中加上tags、categories头,以后hexo new arities时就可自动调出
```
title: {{ title }}
date: {{ date }}
tags:
categories:
```


## 归档页面分类
- 学习资料（网页中一些好的资料为我所用，加上自己的心得运用）
- 笔记总结(自己实践总结的笔记)
- CTF（介绍ctf及writeup）
- 技术文章（原文转载）
- 实践运用（实操运用）

## 标签分类
- python
- kali
- CTF
- PHP
- Linux
