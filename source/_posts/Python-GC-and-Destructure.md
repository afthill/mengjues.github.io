layout: post
title: "Python GC and Destructure"
date: 2015-05-15 18:00:00
tags:
- GC
- Destruction
categories:
- 内核
---
先看一个示例：

```
In [1]:

class Foo:
    def __init__(self, x):
        print "Foo: Hi"
        self.x = x
    def __del__(self):
        print "Foo: bye"

class Bar:
    def __init__(self):
        print "Bar: Hi"
        self.foo = Foo(self) # x = this instance

    def __del__(self):
        print "Bar: Bye"

bar = Bar()
# del bar # This doesn't work either.
Bar: Hi
Foo: Hi
In [2]:

import sys
In [3]:

sys.getrefcount(bar)
Out[3]:
3
```

这里的destructor在对象被销毁的时候并没有被调用，原因就是：

- Cyle Reference
- CPython的实现在对象的GC是基于简单的reference count

即使我们使用`del bar`这样的操作，也不会触发destructor，原始是：

- 当前的对象的reference count是3
- del只会让reference count减1

根据文档：

> "del x" doesn't directly call `x.__del__()` — the former decrements the reference count for x by one, and the latter is only called when x's reference count reaches zero. Some common situations that may prevent the reference count of an object from going to zero include: circular references between objects

也就是说`x.__del__`跟`del x`不是同义的，并且根据文档，如果在cycle reference的对象
中使用`__del__`将会造成内存泄露：

> Circular references which are garbage are detected when the option cycle detector is enabled (it's on by default), but can only be cleaned up if there are no Python-level `__del__()` methods involved.

> A list of objects which the collector found to be unreachable but could not be freed (uncollectable objects). By default, this list contains only objects with `__del__()` methods.26.1Objects that have `__del__()` methods and are part of a reference cycle cause the entire reference cycle to be uncollectable, including objects not necessarily in the cycle but reachable only from it. Python doesn't collect such cycles automatically because, in general, it isn't possible for Python to guess a safe order in which to run the `__del__()` methods. […] It's generally better to avoid the issue by not creating cycles containing objects with `__del__()` methods

### 程序退出时，destructor会被调用吗？

[答案](http://www.python.org/search/hypermail/python-1993/0109.html)是不确定。

> One final thing to ponder: if we have a `__del__` method, should the interpreter guarantee that it is called when the program exits? (Like C++, which guarantees that destructors of global variables are called.) The only way to guarantee this is to go running around all modules and delete all their variables. But this means that `__del__` method cannot trust that any global variables it might want to use still exist, since there is no way to know in what order variables are to be deleted.

### Exception in `__del__`

```
def __del__(self):
    raise Exception("Oopsy")
    print "Bar: Bye"

Exception exceptions.Exception: Exception('Oopsy',) in > ignored
Bar: Hi
Foo: Hi
Foo: bye
```

会被忽略，只是编译器会有warning。

### 修正后的版本：

```
import weakref

class Foo:
    def __init__(self, x):
        print "Foo: Hi"
        self.x = weakref.ref(x)
    def __del__(self):
        print "Foo: bye"

class Bar:
    def __init__(self):
        print "Bar: Hi"
        self.foo = Foo(self) # x = this instance

    def __del__(self):
        print "Bar: Bye"
```

### 参考：

[1] <http://mindtrove.info/python-weak-references/>
