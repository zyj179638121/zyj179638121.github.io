---
layout: post
title: "Swiftype添加站内搜索"
date: 2015-10-1 16:59:23 +0800
comments: true
categories: 
---

## 利用swiftype为hexo添加站内搜索

有了博客，写了辣么多文章没有站内搜索功能怎么行！废话不说开始吧。

### 到swiftype网站注册账号

首先，要去swiftype的网站[https://swiftype.com](https://swiftype.com)注册一个账号，然后根据指引建立好自己网站对应的索引。注册以后有30天高级账户的使用（那种有可以建立多个搜索引擎、多账户共同管理、一个引擎index多个域名的功能的账户）。

<!--more-->

#### 创建一个引擎
![](http://i5.tietuku.com/ac4a8c51a6325812.png)

#### 给自己的网站建立一个索引
![](http://www.jerryfu.net/img/search-engine-for-hexo-with-swiftype-v2/2.jpg)

#### 输入自己网站的网址
![](http://i5.tietuku.com/aa499a1200b4e7ac.png)

#### 给创建的搜索引擎起个名字
![](http://i5.tietuku.com/a29b2d4d88f6c370.png)

#### 添加引擎成功
![](http://i12.tietuku.com/5243bceb97eba65a.png)

### 添加配置

![](http://www.jerryfu.net/img/search-engine-for-hexo-with-swiftype-v2/9.jpg)

#### 跳转到配置页面

**SEARCH RESULTS 、AUTO COMPLETE、 COLORS改不改随意，不想折腾保持默认即可**

![](http://i12.tietuku.com/795de0220d9b2bf8.png)


#### 设置搜索失败界面
![](http://i12.tietuku.com/36b26daa6290f980.png)

#### 设置搜索结果页面

**如果不配置的话默认的结果展现方式是这个样子的（弹出一个结果展现框）：**

![](http://www.jerryfu.net/img/search-engine-for-hexo-with-swiftype-v2/14.jpg)

**如果想要实现单开一个页面显示搜索结果的话就要进行一下配置**

![](http://i12.tietuku.com/d17740d0b30177a3.png)

**最后点击保存就可以了。接下来选择OVERVIEW**
![](http://i12.tietuku.com/c7a13a10340eb487.png)

**选择Install Search**
![](http://i12.tietuku.com/8532254cdff8371a.png)

#### 复制你的swiftype 搜索引擎的 key

![](http://i5.tietuku.com/f54d8f83f2625c6f.png)

### 最后

然后，编辑 站点配置文件，新增 swiftype_key 字段，值为你的 swiftype 搜索引擎的 key。
