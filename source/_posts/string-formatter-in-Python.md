title: Python处理string的formatter的三种方法
date: 2016-01-04 09:43:55
tags:
- String Formatter
categories:
- Python

---

### 参考

<https://zerokspot.com/weblog/2015/12/31/new-string-formatting-in-python/>

三种方法问别为：
- % 操作符
- str.format
- 字串插值

### % 操作符

```
"%s %s" % ("Hello", "World")
```

类似于c语言里面的sprintf。

### str.format()

PEP-3101, since python 2.6，str.format()主要用来处理%操作符不能处理的问题：

```
"{}".format("lala")
>>> 'lala'
"{}".format(("lala", ))
>>> "('lala', )"
```
支持字典：

```
"{firstname} {lastname}".format(firstname="Horst", lastname="Gutmann")
```

PEP-3101引入的新的format协议：

```
class Country:
    def __init__(self, name, iso):
        self.name, self.iso = name, iso

    def __format__(self, spec):
        if spec == 'short':
            return self.iso
        return self.name

country = Country("Austria", "AUT")

print("{}".format(country))
print("{:short}".format(country))
```

上面的特殊的`__format__`将会在`"{!s}".format(country)`时被调用。

```
class date:
    ...
    def __format__(self, fmt):
        if len(fmt) != 0:
            return self.strftime(fmt)
        return str(self)
```

```
import datetime
print("Today is {:%A}".format(datetime.datetime.now()))
# Today is Thursday
```

### PEP-0498: 字串内插值

常见的str.format()方法用起来比较繁琐：

```
a = "Hello"
b = "World"
"{} {}".format(a, b)
# vs.
"%s %s" % (a, b,)
```

PEP-0498引入其他语言（scalar，ruby）已经存在的字串内插：

```
a = "Hello"
b = "World"
f"{a} {b}"
f"{a + ' ' + b}"
```

python中主要使用的是`f`前缀来表达`f-string`。

<--- end -->
