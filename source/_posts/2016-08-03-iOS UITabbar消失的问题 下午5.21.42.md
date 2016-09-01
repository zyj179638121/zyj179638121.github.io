---
layout: post
title: "iOS UITabbar消失的问题"
date: 2016-08-03 15:37:27 +0800
comments: true
categories: 
---
前两天碰到过uitabbar莫名消失的问题,代码里面没有对uitabbar的hidden做处理,折腾了半天,后来发现了问题的所在,是因为在storyboard里面控制tabbar隐藏的时候,本来应该在SB里面UINavigationController对应的属性上面勾上 Hide Bottom Bar on Push,结果我在ViewController对应的属性上勾上了此选项...