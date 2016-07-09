layout: post
title: "Python ByteCode in Details"
date: 2015-04-22 17:22
tags:
- ByteCode
categories:
- Python
---

### First Look of bytecode

我们先来看一个代码：

```
class A:
  def __init__(self):
    pass
  def __repr__(self):
    return 'A()'
a = A()
print a
```

很容易理解，首先声明一个old style的类A，里头声明了2个方法，然后把A初始化成对象并赋值给a，最后打印a对象。

我们可以把这段代码编译成python code对象：

```
co2 = compile("""class A:
  def __init__(self):
    pass
  def __repr__(self):
    return 'A()'
a = A()
print a""", "class-A-input", "exec")

co2
Out[114]: <code object <module> at 00000000040B8630, file "class-A-input", line 1>
```

然后我们使用内置的dis模块把生成的co2对象显示为opcode：

```
import dis

dis.dis(co2)

  1           0 LOAD_CONST               0 ('A')
              3 LOAD_CONST               3 (())
              6 LOAD_CONST               1 (<code object A at 00000000040B8530, file "class-A-input", line 1>)
              9 MAKE_FUNCTION            0
             12 CALL_FUNCTION            0
             15 BUILD_CLASS         
             16 STORE_NAME               0 (A)

  6          19 LOAD_NAME                0 (A)
             22 CALL_FUNCTION            0
             25 STORE_NAME               1 (a)

  7          28 LOAD_NAME                1 (a)
             31 PRINT_ITEM          
             32 PRINT_NEWLINE       
             33 LOAD_CONST               2 (None)
             36 RETURN_VALUE        
```

以上就是在python编译好的bytecode，以便后面在python VM中运行。

#### ByteCode解析：

- 最左边的一列（1， 6， 7）是源代码里的行号，比如1就代表的是第1行。
- 第二列（0，3,6,9……36）表示的是OPCODE表的index
- 第三列（LOAD_CONST，MAKE_FUNCTION）是具体的OPCODE，这个OPCODE只是用来帮助人类阅读用的，python VM执行的还是前面的index
- 第四列（0,3,1）等指的是OPARGS的index，这个index不是从一个全局表里取的，而是基于前面的OPCODE
  - 比如：`LOAD_CONST`是从`co_consts`中取，`LOAD_NAME`是从`co_conames`中取
- 第五列是OPARGS的辅助提示，只是用来帮助人类阅读


##### 参考：

1. <http://security.coverity.com/blog/2014/Nov/understanding-python-bytecode.html>
2. <http://akaptur.com/blog/categories/python-internals/>
3. <http://eli.thegreenplace.net/tag/python-internals>
