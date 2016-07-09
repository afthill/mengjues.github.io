layout: post
title: "ByteCode Instrumentation"
date: 2015-04-28 13:05
tags:
- ByteCode
categories:
- 技术
---

### Bytecode Instrumentation 定义

我们首先来看看，什么叫“Instrumentation”？Instrumentation这个词有很多意思，在维基百科中，它是这样解释的：

> **Instrumentation** is the use of measuring instruments to monitor and control a process. It is the art and science of measurement and control of process variables within a production, laboratory, or manufacturing area.

似乎中文中没有一个合适的词来描述，意思就是“使用计量器具去监控和控制一个流程”就可以被叫做“Instrumentation”。比如下面这个很
直观的器具就被用来做Instrumentation了。

![Instrumentation](https://upload.wikimedia.org/wikipedia/commons/thumb/7/76/Steuerstand01.jpg/160px-Steuerstand01.jpg)

#### 什么是ByteCode Instrumentation

> Bytecode instrumentation (BCI) is a technique in which bytecode in injected directly into a Java class to achieve some purpose that the class did not originally support.

这是我在[网上](http://www.ibm.com/developerworks/cn/java/j-rtm1/index.html)<sub>[2]</sub>(http://www.ibm.com/developerworks/cn/java/j-rtm2/index.html)看到的一个定义，里面对“bytecode instrumentation”的翻译就是“字节码插装”，但是这里有个缺陷就是，针对字节码的插装，并不只限于Java语言，其他基于字节码的语言（比如.Net/Python）也是可以插装的。

而且可以进一步延伸的是，除了有字节码插装（bytecode instrumentation）之外，也有源代码级别的插装（sourcecode instrumentation）。他们的使用场景是不一样的，比如字节码插装主要发生在程序运行时，当然编译时也是可以字节码插装的。而源代码级别的插装主要是发生在编译时。

### 参考：
1. <http://www.ibm.com/developerworks/cn/java/j-rtm2/index.html>
2. <https://technoga.wordpress.com/2012/12/01/what-is-bytecode-instrumentation/#2>
3. <https://github.com/neuroo/equip>
4. <http://security.coverity.com/blog/2014/Nov/understanding-python-bytecode.html>
