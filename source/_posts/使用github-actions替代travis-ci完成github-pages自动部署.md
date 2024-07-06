---
title: 使用github actions替代travis-ci完成github pages自动部署
tags:
  - Hexo
  - github actions
categories:
  - Hexo
abbrlink: 9d0df432
date: 2024-07-06 13:33:17
---

前段时间发现之前使用github pages构建的[博客](https://fuos.github.io/my-blog/)自动化失败了，看了下原因是travis-ci不能白嫖了。于是就重新建了一个[博客](https://fuos.github.io/)。

## 博客组成  
`GitHub Pages`：静态网站托管服务  
`Hexo`：一个快速、简单、高效的博客框架  
`GitHub Actions`：持续集成和持续交付（CI/CD）平台  
网站首页：fuos.github.io/  
## 搭建过程
每次重新搞博客都很费劲，简单记录📝下搭建的过程：

### 1.环境配置
本地安装nodejs，git，配置ssh

### 2.安装Hexo
新建文件夹📂my-blog，进入后执行：
```bash
# 安装Hexo
npm install -g hexo-cli
# 初始化博客
hexo init hexoblog
cd hexoblog
# 安装依赖
npm install
```
### 3.安装主题
本次使用的是[hexo-theme-keep](https://github.com/XPoet/hexo-theme-keep)主题，如何下载、使用、配置都在文档里，写的非常清楚。

### 4.创建github repo
创建repo为`fuos.github.io`，fuos为用户名，不要readme.md。切换到actions，修改Settings->Pages中Build and deployment选项为github actions，保存即可。

### 5.推送代码
将代码推送到刚创建的github repo，注意是main分支。
```bash
cd hexoblog 
git init 
git remote add origin git@github.com:fuos/fuos.github.io.git  
git add . 
git commit -am "init blog" 
git push -u origin main  
```
### 6.配置GitHub Actions
点击repo里面的Actions->new workflow，新建一个pages.yml文件，文件内容copy这里：https://hexo.io/docs/github-pages

### 7.Hexo常用命令
进入博客根目录hexoblog执行：
```bash
# 发表文章
hexo n "hello word"
# 清除缓存文件db.json和生成的文件public
hexo clean
# 生成静态文件
hexo g
# 启动服务
hexo s
# 部署网站
hexo d
```
