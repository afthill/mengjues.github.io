title: What does the concurrent really mean?
date: 2017-09-10 01:56:51
tags:
- concurrent
categories:
- 内核
---

最近看到一本书上写的一个东西，对“并发”有了一个更好的理解：之前我们理解的并发（concurrency）从字面意思上是指“多个事情同时发生就是所谓的并发”，从这本书里看到一个更好的解释，感觉很合理：如果我们不能简单知道多个事件（event）的运行顺序（happens before），那我们就可以把这个称之为并发。

参考：<http://greenteapress.com/semaphores/LittleBookOfSemaphores.pdf>
