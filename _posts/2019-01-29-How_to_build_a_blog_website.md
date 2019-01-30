---
title: "使用 GitHub Pages 搭建个人网站"
permalink: /how-to-build-a-blog-website/
date: 2019-01-29
tags: [github, 个人网站]
header:
    images: "/images/perceptron/percept.jpg"
excerpt: "java, web, aliyun, domain, github, pages, jekyll, minimal-mistakes"
---

[toc]


## 绑定域名
### 项目内配置文件

在项目根目录下新建 `CNAME` 文件，在文件中添加域名信息。如个人域名为：`www.kandigx.com`，那么应该添加 `kandigx.com`。不要添加 `www`前缀。

### Github 项目设置
在 Github 仓库的 setting 设置中，在 `GitHub Pages` 部分，`Custom domain`中填入希望能代替原`kandigx.github.io`的域名。

[//]: # (哈哈我是注释，不会在浏览器中显示。)


### 域名解析设置
阿里云域名解析设置
添加记录，如果提示已存在，则删除原 `wwww` 或者 `@` 的主机记录项。
记录类型：CNAME，主机记录： @  ，记录值： kandigx.github.io
记录类型：CNAME，主机记录：www ，记录值： kandigx.github.io
添加这两条记录即可。`@`类型直接解析主域名，即我的 kandigx.com，`www`类型解析带 `www`前缀的域名，即 www.kandigx.com