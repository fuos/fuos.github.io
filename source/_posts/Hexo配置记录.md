---
title: Hexo配置记录
abbrlink: a2ce8d15
date: 2024-07-30 16:55:17
tags:
  - Hexo
categories:
  - 建站
cover: https://fuos.github.io/picx-images-hosting/20240730/Hexo-config.4g4edkaikh.webp
---

## 主题

### 在用主题

hexo-theme-keep : https://github.com/XPoet/hexo-theme-keep

整体好用。就是每次更新文章最后更新日期都被刷成了最新，虽然在github actions提供了解决方案，但是其他平台没有。会折腾的可以玩。

hexo-theme-butterfly : https://github.com/jerryc127/hexo-theme-butterfly

整体好用。布局基本能覆盖上个主题，定制化强，魔改可以很魔幻。特点是图片很多，不加不好看，加了速度慢。会搞优化的很推荐玩。

目前发现的bug：配置giscus不能显示最新评论。所以我改成了默认waline。也有可能是giscus不支持。

这两个主题都有配置，可能随时切换，配置文件在[这里](https://github.com/fuos/fuos.github.io)。

### 用过的主题

hexo-theme-indigo : https://github.com/yscoder/hexo-theme-indigo

简约风格。很久之前用过的一个主题，特点是简约。不过现在已经好久没更新了，node和其他模块版本都比较老旧，不推荐。

之前的配置在[这里](https://github.com/fuos/my-blog)。

### 推荐主题

一般会在这两个网站上找：一个就是Hexo官方[themes](https://hexo.io/themes/)，一个是[statichunt](https://statichunt.com/hexo-themes)，多找一找，总有你喜欢的。优先选择star🌟多的，最近更新时间在1年以内的，太老的就别用了。。。

## 字体

```yml
# 文章字体：霞鹜文楷
- <link rel="stylesheet" href="https://cdn.staticfile.net/lxgw-wenkai-screen-webfont/1.7.0/style.css">
# 代码字体：FiraCode
- <link rel="stylesheet" href="https://cdn.staticfile.net/firacode/6.2.0/fira_code.css">
```

## 评论

[Giscus](https://giscus.app/zh-CN)：基于github Discussions。不知道为什么控制台会显示404，但是又不影响使用。

[Waline](https://waline.js.org/)：基于[LeanCloud](https://console.leancloud.app/login)+[vercel](https://vercel.com/)免费部署。

## 域名

目前使用的都是[us.kg](https://register.us.kg/panel/main)免费域名，托管到cf上，用的丐版，没有开启小黄云。

## 托管

代码在github，使用github actions自动编译。同时一键部署到cloudflare和vercel平台，都可以自动部署。速度上还是vercel快一些，但是有些地方是红色的。cf和gh pages总体速度不是最快，但基本所有地区都可以访问。

### 主站在vercel
https://blog.bitmap.us.kg/	（fast）

### cloudflare pages
https://cf.bitmap.us.kg/	（stable）

### github pages
https://gh.bitmap.us.kg/	（stable）

## 优化

优选ip加速：
cloudflare、vercel或netlify加速：[enhanced-FaaS-in-China](https://github.com/xingpingcn/enhanced-FaaS-in-China)
vercel加速：[Vercel-CDN](https://github.com/Fgaoxing/Vercel-CDN)
改国内cname：
vercel针对中国大陆用户的cname：`cname-china.vercel-dns.com`。

## 搜索
最开始使用本地搜索，后来换成了algolia，创建[应用](https://dashboard.algolia.com/account/applications)，然后创建[索引](https://dashboard.algolia.com/apps/J1V57O494N/indices)，绑定到hexo主题的配置文件就就行。

因为配置了algolia，所以每次发布文章都要建立索引，本地启动测试三连变四连：
```bash
hexo cl && hexo g && hexo algolia && hexo s --open
```

## 工具

推荐几个自己写博客常用的工具。

### 图床

目前使用的是[picX](https://picx.xpoet.cn/#/upload)，内置压缩，水印，图片托管在github，无需安装，github登录直接用，建议使用github pages模式。

也可以自建免费图床，比如基于[telegraph](https://github.com/x-dr/telegraph-Image)的图床，但是限制较多，图片大小，格式，速度等。

这里是一个测试图片：
![c6b55521100c7b75c935b.jpg](https://img.bitmap.us.kg/file/c6b55521100c7b75c935b.jpg)

### 编辑器
这玩意儿有很多，我自己用的是[stackedit](https://stackedit.io/app#)

### 封面制作
简单的一个博客封面制作工具：[coverview](https://coverview.vercel.app/editor)

### 图片压缩
推荐[tinypng](https://tinypng.com/)，[caesium](https://caesium.app/)，这俩基本就够用了，第二个还有app可以下载。

### 网站配色
推荐一个在线网站[gradients.app](https://gradients.app/zh/mesh)，可以选取自己喜欢的颜色，然后自定义注入样式。

完整配置见[这里](https://github.com/fuos/fuos.github.io)，博客开源，代码和配置都开源，有些配置搞不明白也可以去看看，欢迎来噶🔪。