layout: post
title: "Meta Class in Python"
date: 2015-05-12 10:52:28
tags:
- Meta Class
categories:
- 程序语言
---

### About Meta Class

由于class在Python中是first class对象，所谓的“first class”是指class跟Python中的其
他对象是同样级别的，比如：

- class可以在运行时动态创建
- class可以作为函数的参数名被传入
- class可以重新赋值
- class可以作为被return，作为返回的值

我们通常意义上理解的class是对象的模具或者原型，是对对象的抽象；我们可以通过初始化
class生成基于该class的对象，在python中，我们叫这个原型为type（类型）。对于类型的
理解，由于Python中一切都是对象，所以type本身也是对象，对于type的划分一般可以分为：

- 在标准库中存在的type
- 用户自定义的type

我们只关注用户自定义的type，Python在标准库中提供了一个原型type，然后所有的新型的
Python的class都是基于type创建的，我们可以使用下面的代码证明：

```
class C(object):
    pass

type(C)
# type
```

值得注意的是，我们这里的class在定义的时候，传入了一个object，这个表明该class继承
了object类，object不是type，object同样由type生成的。这个C类这里继承object主要是
为了继承object里那些特殊的方法：

```
'__class__', '__delattr__', '__doc__', '__format__', '__getattribute__',
 '__hash__', '__init__', '__new__', '__reduce__', '__reduce_ex__',
 '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__'
 ```

所有的Python中的class对象都是type生成的。如果不想使用type，那可以选择我们这里说
的Meta Class，大部分Meta Class继承于type。

<!--more-->

### Meta Class Generation

有两种方法，一种是type()函数，动态生成；另外一种是继承type对象。

#### type()函数

```
type(class_name, (bases,), kwattrs)
```

比如我们可以使用`type('MyKlass', (object,), {"foo": "bar"})`去生成一个class，名字
叫做`MyKlass`，包括一个类属性`MyKlass.foo = "bar"`，可以用如下代码验证：

```
In [2]:

MyKlass = type('MyKlass', (object,), {"foo": "bar"})
In [3]:

MyKlass
Out[3]:
__main__.MyKlass
In [4]:

MyKlass.foo
Out[4]:
'bar'
```

上面的代码等同于如下：

```
Class MyKlass(object):
    foo = 'bar'
```

#### Meta Class的`__new__`、`__init__`

Meta Class在Python的定义为：class of class。如何理解？看如下我们使用了Meta Class
后的示例：

```
In [7]:

class Meta(type):
    pass
class A(object):
    __metaclass__ = Meta
type(A)
Out[7]:
__main__.Meta
```

如果不使用Meta Class：

```
In [9]:

class A(object):
    pass
type(A)
Out[9]:
type
```

我们可以看到，生成class A的对象从Meta变成了type。所以，在Python引入Meta Class的
原因就是，用来控制class的生成。其实，Meta Class类跟其他的Python中的类一样，也有
两个特殊的方法（`__new__、__init__`），用来初始化类的生成。

+ `__new__`是用来控制class的创建
+ `__init__`是用来控制class的初始化

因此，对于如下代码：

```
MyKlass = MyMeta.__new__(MyMeta, name, bases, dct)
MyMeta.__init__(MyKlass, name, bases, dct)
```

表现了class被创建的过程。

```
class MyMeta(type):
    def __new__(meta, name, bases, dct):
        print '-----------------------------------'
        print "Allocating memory for class", name
        print meta
        print bases
        print dct
        return super(MyMeta, meta).__new__(meta, name, bases, dct)
    def __init__(cls, name, bases, dct):
        print '-----------------------------------'
        print "Initializing class", name
        print cls
        print bases
        print dct
        super(MyMeta, cls).__init__(name, bases, dct)
```
测试代码：

```
class MyKlass(object):
    __metaclass__ = MyMeta

    def foo(self, param):
        pass

    barattr = 2
```

#### Meta Class `__call__`

+ `__init__`、`__new__`是在class被创建的过程中被调用，而`__call__`是在被创建的
class初始化一个新的对象时，被调用：

```
class MyMeta(type):
    def __call__(cls, *args, **kwds):
        print '__call__ of ', str(cls)
        print '__call__ *args=', str(args)
        return type.__call__(cls, *args, **kwds)

class MyKlass(object):
    __metaclass__ = MyMeta

    def __init__(self, a, b):
        print 'MyKlass object with a=%s, b=%s' % (a, b)

print 'gonna create foo now...'
foo = MyKlass(1, 2)
```

输出：

```
gonna create foo now...
__call__ of  <class '__main__.MyKlass'>
__call__ *args= (1, 2)
MyKlass object with a=1, b=2
```

### Meta Class真实使用案例

#### twisted.python.reflect.AccessorType

> Metaclass that generates properties automatically. Using this metaclass for your class will give you explicit accessor methods; a method called set_foo, will automatically create a property 'foo' that uses set_foo as a setter method. Same for get_foo and del_foo.

然后我们来看实际代码：

```
class AccessorType(type):
    def __init__(cls, name, bases, attrs):
        super(type, cls).__init__(cls, name, bases, attrs)
        accessors = {}
        prefixs = ["get_", "set", "del"]
        for k in attrs.keys():
            v = getattr(cls, k)
            for i in range(3):
                if k.startswith(prefixs[i]):
                    accessors.setdefault(k[4:], [None, None, None])[i] = v
        for name, (getter, setter, deler) in accessors.items():
            setattr(cls, name, property(getter, setter, deler, ""))
```

#### pygments Lexer and RegexLexer

注：Lexer的意思是“词法分析”，在一些解析程序（Parser）经常会看到。

pygments对Meta Class的使用与django类似，就是使用一个Base类，该类使用Meta Class，
然后后面的所有的类继承于该Base类，顺便也获得了Meta Class属性：

```
class LexerMeta(type):
    def __new__(cls, name, bases, attrs):
        if 'analyse_text' in attrs:
            attrs['analyse_text'] = make_analysator(d['analyse_text'])
        return type.__new__(cls, name, bases, attrs)
```

这里使用了`__new__`跟`__init__`相比，没有很大的差别，只是语义上有区分。还有
`__new__`在调用时，必须使用return用来返回新生成的对象，而`__init__`用来初始化，
则不是很需要。

##### `__call__`的使用

`__call__`与`__new__, __init__`的不同在于，前者是在Meta生成的class在生成对象的时
候被调用，后者是class被创建时调用。

```
class RegexLexerMeta(LexerMeta):
    def __call__(cls, *args, **kwargs):
        if not hasattr(cls, "_tokens"):
            cls._all_tokens = {}
            cls._tmpname = 0
            if hasattr(cls, "token_variants") and cls.token_variants:
                pass
            else:
                cls._tokens = cls.process_tokendef('', cls.tokens)

        return type.__call__(cls, *args, **kwargs)
```

- `__call__`只会被执行一次，与其他的类方法`__init__, __new__`一样
- `__call__`的是使用，可以减少loading时的负担

### 参考：

[1] <http://eli.thegreenplace.net/2011/08/14/python-metaclasses-by-example>
