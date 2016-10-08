title: threadsafe caching in Python
date: 2016-10-09 01:10:59
tags:
- Python
categories:
- 程序语言
---

原文：https://noamkremen.github.io/a-simple-threadsafe-caching-decorator.html

这里在做CPU-bound运算的时候，想要缓存第一次计算的结果，但是functools模块里提供的lru_cache并不是threadsafe的，所以没有办法在多线程环境使用。

下面是新的实现，借用了CPython中dict的插入和查询是threadsafe的前提：

```
import threading
from collections import defaultdict
from functools import lru_cache, _make_key

def threadsafe_lru(func):
    func = lru_cache()(func)
    lock_dict = defaultdict(threading.Lock)

    def _thread_lru(*args, **kwargs):
        key = _make_key(args, kwargs, typed=False)  
        with lock_dict[key]:
            return func(*args, **kwargs)

    return _thread_lru
```
