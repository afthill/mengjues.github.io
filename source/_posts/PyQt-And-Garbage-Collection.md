layout: post
title: "PyQt: The Garbage Collection"
date: 2015-04-28 17:55
tags:
- PyQt GC
categories:
- 程序语言
---

最近在写一个PyQt程序的时候，遇到一个奇怪的现象，slot或者process总是奇怪地消失了，但是调试信息里什么也不会提示，经过艰苦的
排查后，发现了如下的问题：

#### 参考：
1. http://stackoverflow.com/questions/20035604/how-to-prevent-python-garbage-collection-for-anonymous-objects
2. http://ralsina.me/weblog/posts/BB990.html
3. http://stackoverflow.com/questions/31286855/pyqt-drops-all-of-signals-and-slots-after-using-chained-invoke-like-pyobj-setupu
4. http://stackoverflow.com/questions/17211797/does-pyqt4-signal-connect-keep-objects-live
