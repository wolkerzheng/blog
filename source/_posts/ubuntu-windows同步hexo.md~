---
title: ubuntu&windows同步hexo
date: 2016-12-13 19:33:37
tags: learn
categories: 杂
---

在windows和ubuntu上根据网上相关教程均已配好环境。
## windows上
使用git
在hexo初始化所在的博客文件夹下初始化仓库：
	`git init` 
然后在github上new 一个 repositories,假设它的地址为:git@github.com:user/blog.git,输入
	`git remote add origin git@github.com:user/blog.git`

添加本地所有文件到仓库,并添加更新说明：
	`git add .`
	`git commit -m 'commit'`
同步到git上取：
	`git push -u origin master`


## ubuntu

假设这时候已经将windows上的内容备份到git上，

在ubuntu下新建一个文件夹：
	`mkdir blog`

在blog中初始化仓库：
	`git init `
将本地文件和线上仓库映射起来
	`git remote add origin git@github.com:user/blog.git`
从网上拉取内容,reset是不做任何合并处理，强制将本地内容指向刚刚同步下来的云端内容：
	`git fetch --all
	 git reset --hard origin/master`


## 更新文章的同步操作

在ubunut下写完文章后更新上去
	`git add .
	 git commit -m '更新内容'
	 git push`

在windows上使用：
	`git pull`
