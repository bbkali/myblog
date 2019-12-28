---
title: Github使用总结
date: 2019-03-04 14:36:14
tags: Github
categories:
    - 学习资料
    - Github
---

## 安装git
[官网地址](https://git-scm.com/downloads)
- 安装完成后，还需要最后一步设置，在命令行输入：
```bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

## 快速开始
1. 创建版本库。版本库又名仓库，英文名repository，你可以简单理解成一个目录（.git），这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪。
```bash
git init
```
2. 将新建文件提交到仓库：
```bash
git add your_add_file_name   #将新文件添加到索引
git commit -m "你的提交描述说明"
```
<!--more-->
__工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库,Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向master的一个指针叫`HEAD`__
[![分析图](https://s2.ax1x.com/2019/03/05/kXBsmV.jpg)](https://imgchr.com/i/kXBsmV)
1. `git add`把文件添加进去，实际上就是把文件修改添加到暂存区
2. 用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支


## 远程仓库
1. 创建SSH Key。在用户主目录下，看看有没有.ssh目录及id_rsa和pub_rsa这几个文件，如果没有需要创建
```bash
 ssh-keygen -t rsa -C "youremail@example.com"
```
2. 登陆GitHub，打开“Account settings”，“SSH Keys”页面,将pub_rsa内容粘贴进去

## 帮助文档
|命令|使用方法|描述|
|:----:|:----------:|:-------------------:|
|clone|git clone "仓库链接"|将存储库克隆到新目录中|
|init|git init|创建空的Git存储库或重新初始化现有的Git存储库|
|add|git add *|将文件内容添加到索引|
|mv|git mv -f file newfile|移动或重命名文件、目录或符号链接|
|reset|git reset -\-hard HEAD^|将当前磁头重置为指定状态|
|rm|git rm your_file|从工作树和索引中删除文件|
|bisect||使用二进制搜索查找引入错误的提交|
|grep|git grep your_wordkey|打印与图案匹配的线条|
|log|git log --oneline|显示提交日志|
|show|git show tagname|显示各种类型的对象|
|status|git status|显示工作树状态,时刻掌握仓库当前的状态|
|branch|git branch|列出、创建或删除分支|
|checkout|git checkout -\- file或git checkout your_branch|切换分支或还原工作树文件|
|commit|git commit -m "your_der"|记录对存储库的更改|
|diff|git diff file|显示提交、提交和工作树等之间的更改|
|merge|git merge your_branch|将两个或多个发展历史连接在一起|
|rebase|git rebase|把分叉的提交历史“整理”成一条直线，看上去更直观|
|tag|git tag|创建、列出、删除或验证用GPG签名的标记对象|
|fetch|git fetch origin 远程分支:本地分支|从另一个存储库下载对象和引用|
|pull|git pull|从另一个存储库或本地分支获取并与之集成|
|push|git push origin master|更新远程引用和关联对象|
## 常用命令

1. git status 查看当前状态
2. git log 查看提交日志
>commit的所有记录,git log --graph --pretty=oneline --abbrev-commit 可以简单输出
3. git reflog 查看命令历史
>reflog 是可以查看历史命令,找到未来版本号,用--hard回到未来版本
4. git reset -\-hard HEAD^
>HEAD^为上一个版本 HEAD^^为上上一个版本 HEAD~100为上一百个版本
5. git checkout -\- yourfile
>场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库
6. git remote add origin git\@github.com:yourname/yourrepository.git
> 添加远程仓库
7. git push master origin 
> 将本地文件推送到远程仓库
8. git checkout -b dev
> 表示创建并切换分支
9. git merge dev
> 用于合并指定分支到当前分支
10. git branch -d dev
> 删除当前分支
11. 分支常用命令
```
Git鼓励大量使用分支：

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>
```

12. git stash 
>可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作,需要先把文件git add
 是用git stash apply恢复 但是恢复后，stash内容并不删除你需要用git stash drop来删除
 git stash pop就是直接恢复最近的并删除stash
 git stash list 可以查看stash列表

13. git checkout -b dev origin/dev
 > 要在dev分支上开发，就必须创建远程origin的dev分支到本地，用这个命令创建本地dev分支

14. git 标签总结
>命令git tag <tagname>用于新建一个标签，默认为HEAD，也可以指定一个commit id；
命令git tag -a <tagname> -m "blablabla..."可以指定标签信息；
命令git tag可以查看所有标签。
命令git tag <tagname> <HEAD> 对指定版本打标签
命令git push origin <tagname>可以推送一个本地标签；
命令git push origin --tags可以推送全部未推送过的本地标签；
命令git tag -d <tagname>可以删除一个本地标签；
命令git push origin :refs/tags/<tagname>可以删除一个远程标签

## 补充
1. 配置忽略文件.gitignore,主要是一些个人的配置文件,禁止上传，格式如下
```
*.pyc
*.ini
*.so
*.egg
*.egg-info
dist
build
```
> git add -f your_file 可以强制上忽略的文件
> git check-ignore -v your_file 可以查看文件忽略的状态

2. 配置别名
设置自己习惯的命令来git，格式如下
```bash
git config --global alias.<your_commond> <commond>
会配置如下
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
$ git config --global alias.unstage 'reset HEAD'
$ git config --global alias.last 'log -1'
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# 其实以上命令设置,每个仓库设置都存储在.git/config文件中,当前用户的配置放在.gitconfig中,可手动删除不需要的alias
$ cat .git/config 
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com:michaelliao/learngit.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
[alias]
    last = log -1
------------------------------------------------------------------------------
$ cat .gitconfig
[alias]
    co = checkout
    ci = commit
    br = branch
    st = status
[user]
    name = Your Name
    email = your@email.com
```

3. 贡献公共仓库
```
1. fork别人项目到自己的仓库
2. git clone 项目到本地
3. git checkout -b 新分支上修改项目
4. push 到远程项目分支
5. new pull request 等待别人采用
```