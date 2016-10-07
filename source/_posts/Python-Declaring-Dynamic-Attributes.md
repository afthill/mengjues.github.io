title: 'Python: Declaring Dynamic Attributes'
date: 2016-10-08 01:25:59
tags:
- Python
categories:
- 程序语言
---

原文：<http://amir.rachum.com/blog/2016/10/05/python-dynamic-attributes/>

利用了python里的3个特殊的方法：`__getattr__` `__setattr__` `__dir__`实现了访问字典时可以像属性（attribution）一样被访问。

这个feature好像是模拟Enum时有用。