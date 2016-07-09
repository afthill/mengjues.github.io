layout: post
title: "Selion自动化测试框架介绍"
date: 2015-04-09 10:08:22
tags:
- Selenium
categories:
- 自动化
---

发现的一个好玩的新的基于selenium（browser based automation framework）的新型的框架，
开发和维护者来自于Paypal公司。

项目地址：<https://github.com/paypal/SeLion>

#### Selion的基本介绍：

#### 客户端
- 客户端基于TestNG进行管理，并且支持`DataProvider`进行数据注入
- 支持selenium的POP（Page Object Pattern）开发模式，好玩的是支持使用类DSL语言使用YAML
格式预定义page object


#### 服务端
- 服务端基于Grid进行管理，并支持对hub和node的自定义

#### 文档
- <http://paypal.github.io/SeLion/html/documentation.html>

#### 注意
- 该项目依然处于开发状态
