title: pthread on windows
date: 2016-10-08 01:29:21
tags:
- pthread
categories:
- 方案
---

原文：<http://locklessinc.com/articles/pthreads_on_windows/>

这里介绍的主要是[pthread_win32](http://sourceware.org/pthreads-win32/index.html)这个库对pthread在win32平台的实现，比如mutext，rwlock等。