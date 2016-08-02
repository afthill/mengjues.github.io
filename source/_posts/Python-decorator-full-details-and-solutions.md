title: Python decorator full details and solutions
date: 2016-08-02 10:43:32
tags:
- Python
- Decorator
categories:
- 方案
---

原文： https://hynek.me/articles/decorators/

### What is Decorator

- A way to wrap callable
- to change the default behaviour of callable
- modifying arguments or return values
- caching
- itself is a callable

The default style:

```
def func():
  pass

func = decorator(func)
```

which is identical to the static syntax sugar:

```
@decorator
def func():
  pass
```

### The python standard decorator solution

```
from functools import wraps

def my_decorator(f):
    @wraps(f)
    def wrapper(*args, **kwds):
        print('calling decorated function')
        return f(*args, **kwds)
    return wrapper
```

```
def mult(a, b, c=1):
    return a * b * c;

mult_decorated = my_decorator(mult)
```

#### decorate后的函数签名变化了

```
>>> import inspect
>>> inspect.getargspec(mult)
ArgSpec(args=['a', 'b', 'c'], varargs=None, keywords=None, defaults=(1,))
```

```
>>> inspect.getargspec(mult_decorated)
ArgSpec(args=[], varargs='args', keywords='kwds', default=False)
```

可以发现wrapper的closure变成了原来的func的签名，这个错误的根源来自于functools.wraps。

#### Root Cause

functools.wraps 只取出了并保留原来的func的name和docstring。

```
>>> mult.__name__ == mult_decorated.__name__
True
>>> mult.__doc__ == mult_decorated.__doc__
True
```

### 更坏的出现了

```
class C:
    @my_decorator
    @classmethod
    def cm(cls):
        return 42
```

```
>>> C.cm()
Calling decorated function
Traceback (most recent call last):
  File "<input>", line 1, in <module>
    C.cm()
  File "<input>", line 5, in wrapper
    return f(*args, **kwds)
TypeError: 'classmethod' object is not callable
```            
            
### Solutions

#### Python 3.5 and inspect.signature
inspect.signature 使用了functools.wraps提供的`__wrapped__`获得了正确的函数签名。

#### BOLTONS

使用boltons.funcutils替换functools

#### decorator.py

from decorator import decorator

#### wrapt

slowest but most correct!

### 参考：

- https://github.com/GrahamDumpleton/wrapt/tree/master/blog
- http://blog.dscpl.com.au/2014/01/how-you-implemented-your-python.html

