---
title: Deploy with Cloudflare Pages
tags:
  - Hexo
  - cf
categories:
  - 建站
abbrlink: 49749a97
date: 2024-07-12 17:45:00
---
cf pages也支持托管hexo！！！
一键部署github pages到cf：[参考链接](https://developers.cloudflare.com/pages/framework-guides/deploy-a-hexo-site/)  ，跟着文档选择你已经部署好的代码，然后：
### 1.修改构建配置
![image](https://fuos.github.io/picx-images-hosting/20240712/image.ese9e6tdm.webp)
参考链接里面没有`npm install hexo-cli -g`，需要加上否则不会生成静态页面。
### 2.设置环境变量
![image](https://fuos.github.io/picx-images-hosting/20240712/image.2yy8m13cfy.webp)
### 3.保存并部署
然后等一等，刷新页面就能看到部署成功了。还可以绑定域名，之后可以通过新的地址访问：[cf-pages测试地址](https://cf.bitmap.us.kg/)