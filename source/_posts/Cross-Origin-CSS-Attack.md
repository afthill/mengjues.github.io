layout: post
title: "CSS跨站攻击原理与防护"
date: 2015-11-24 17:25:00
tags:
- CSS Attack
- CSRF
categories:
- 安全
---


### 参考：

<https://www.linshunghuang.com/papers/css.pdf>

### 基础知识

1. 一个展示在用户面前的页面是由多个不同内部的文档片段组成的：
    - Script
    - CSS
    - IMG

2. 浏览器的基本功能：
    - 浏览器是用来渲染（Render）页面的
    - 浏览器解析首页的HTML后，根据需要拉取所需的页面片段
    - 浏览器负责图片展示
    - 浏览器负责拉取script
    - 浏览器负责执行script

3. 浏览器的安全措施：
    - 浏览器在沙盒之中运行script
    - 浏览器限制script有限访问本地计算机资源
    - 浏览器限制script有限访问本地环境信息
    - 浏览器通过同源策略控制script访问从网络上拉取的内容
        - 比如不能访问和操作不同源（Cross Origin）的图片
        - 比如不能访问和操作不同源（Cross Origin）的CSS
        - 比如不能访问和操作不同源（Cross Origin）的Cookie
        - 比如不能访问和操作不同源的API（如果要访问，需要API支持CORS）

### CSS入门

1. CSS（Cascading stylesheet）是W3C标准化的一种文档类型，用来定义HTML静态页面的外观（Appearance），而
Javascript一般默认用来定义静态页面的行为（Behaviour）

2. 为了以后的扩展的考虑，CSS规范强制要求了对CSS的解析采用error-tolerant。
    - 浏览器不能解析CSS时会自动跳过CSS Directive（指令）
    - 浏览器会继续执行后面可以理解的部分（directive）
    - 设计师对这种规则的利用包括：
        - 在最新的页面中使用最新的CSS技术
        - 如果旧的浏览器不能识别这种新的css directive，将会自动忽略（gracefully degrade）

3. CSS解析系统存在的漏洞
    - 从CSS文档传进来的内容不一定全部都是合法的css directive， 比如有可能是一个HTML文档
    - 从CSS文档传进来的非法的内容也有可能被CSS Interpreter尝试解析
        - CSS文档里的内容可能是有害的脚本，会窃取宿主页面所包含的敏感信息
        - 某些宿主的页面可能需要登陆才能展示，但是隐藏在CSS中的有害的脚本可能会立即获得敏感信息即使在没有登录的前提下
            - 原因是这些页面在发起request时，已经在cookie中携带了敏感信息

### 原因分析

- 首先这是一个CSS Specification的bug，因为ERROR-Tolarant Parsing的要求，导致解析器必须能够处理不能识别的css directive
- 解析器允许一个从外部导入的文档（CSS）去覆盖（Override）一个跨源文档内的内容
    - 解析器应该在上下文中要求安全的跨源确认之后再去处理CSS

### 安全防护

- 使用更加严厉的跨源内容处理规则
    - 被firefox，chrome，opera采用

--------

To be continued
