title: Closure vs Class in annotation
date: 2017-09-15 16:00:00
tags:
- Closure
categories:
- 内核
---

关于Closure一般很少有人与Class放在一起对比，今天看到一个说法，深以为然：

- class是“data with operation attached”
- closure是“operation with data attached”

哈哈，基本上说的非常准确，不过这是区别，如果说到共同点：

- class和closure都是一个包含logic和data的对象。

再者，如果从函数式编程的角度来看，区别就大了：

- class强调的mutable或者rebindable state；
- closure强调的是immutability和pure function；

