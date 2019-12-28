---
title: Hexo常用命令
date: 2019-01-26 22:04:01
tags: 
- Hexo
categories: 
- 学习资料
- Hexo
---
[Hexo](https://hexo.io/)! Hexo 是一个快速、简洁且高效的博客框架。 点击 [这里](https://hexo.io/zh-cn/docs/) 查看文档. 如果遇上问题可以查看 [这里](https://hexo.io/docs/troubleshooting.html) 或者到 [GitHub](https://github.com/hexojs/hexo/issues).

## 快速开始

### 新建一个网站

```bash
$ hexo init
```
- 新建一个网站。如果没有设置 folder ，Hexo 默认在目前的文件夹建立网站。
<!--more-->

### 新建一篇文章

```bash
$ hexo new "My New Post"
```
- 会在_post目录下新建一篇空白的md文件
更多查看: [Writing](https://hexo.io/docs/writing.html)

### 运行服务器

```bash
$ hexo server | hexo s
```
- 可以在本地查看网站效果
更多查看: [Server](https://hexo.io/docs/server.html)

### 生成静态文件

```bash
$ hexo generate | hexo g
```

更多查看: [Generating](https://hexo.io/docs/generating.html)
<!--more-->
### 部署网站

```bash
$ hexo deploy | hexo d
```
- 部署之前预先生成静态文件

更多查看: [Deployment](https://hexo.io/docs/deployment.html)

## 其它命令

### 清理缓存

```bash
$ hexo clean
```
- 清除缓存文件 (`db.json`) 和已生成的静态文件 (`public`)
- 在某些情况,尤其是`更换主题`后，如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令

### 列出网站资料

```bash
$ hexo list <type>
```
- type  Available types: page, post, route, tag, category

### 显示hexo版本

```bash
$ hexo version
```

### 调试模式

```bash
$ hexo --debug
```
- 在终端中显示调试信息并记录到`debug.log`当您碰到问题时，可以尝试用调试模式重新执行一次，并提交调试信息到 [GitHub](https://github.com/hexojs/hexo/issues/new)。

### 简洁模式

```bash
$ hexo silent
```
- 隐藏终端信息

### 安全模式

```bash
$ hexo --safe
```
- 在安全模式下，不会载入插件和脚本。当您在安装新插件遭遇问题时，可以尝试以安全模式重新执行

### 显示草稿

```bash
$ hexo --draft
```
- 显示`source/_drafts`文件夹中的草稿文章

### 发表草稿

```bash
$ hexo publish [layout] <filename>
```

## 联合命令

### 生成静态文件并部署到网站中

```bash
$ hexo -g d
```

