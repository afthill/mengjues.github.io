title: Python and ByteCode
date: 2016-07-20 22:48:48
tags:
- ByteCode
categories:
- Python
---

今天在europython 2016看到的一个好看的印度人的演讲，觉得很有意思，记录下来。

作者：Anjana Valill (vakila.github.io)

问题：Why does python code run faster in a function? (http://stackoverflow.com/questions/11241523/why-does-python-code-run-faster-in-a-function)

```
1 # outside_fn.py                     # inside_fn.py
for i in range(10**8):                def run_loop():
  i                                     for i in range(10**8):
                                          i
$ time python3 outside_fn.py          $ time python3 inside_fun.py
real        0m9.185s                  real          0m5.738s
user        0m9.104s                  user          0m5.634s
sys         0m0.048s                  sys           0m0.055s
```

### The awesome stuff your program does

```
source code
      |
      ~
compiler
=> parse tree > abstract syntax tree > control flow graph =>
      |
      ~
byte code
      |
      ~
interpreter
virtual machine performs operations on a stack of objects
```

### What is byte code?

An intermediate representation of your program.

### What the interpreter "sees" when it run your program

Machine code for a virtual machine (the interpreter)

A series of instructions for stack operations.

Cached as .pyc file.

### How can we read it

dis: bytecode disassembler

```
>>> def hello():
      return 'hello'

>>> import dis
>>> dis.dis(hello)

2       0 LOAD_CONST                1 ('hello')
        3 RETURN_VALUE
```

### What does it all mean

```
                                              argument value  
                                                    |
                                        arg index   |
                                             |      |
                operation name               |      |
                        |                    |      |
line #                  |                    |      |
 |                      |                    |      |
 |           offset     |                    |      |
 |               |      |                    |      |
 ~               ~      ~                    ~      ~
 2               0 LOAD_CONST                1 ('hello')
```

### Sample Operations

```
LOAD_CONST(c)           pushes c onto top of stack (TOS)
BINARY_ADD              pops & adds top 2 items, result becomes TOS
CALL_FUNCTIONS(a)       call function with arguments from stack
                        a indicates # of positional & keyword args
```

### What can we dis                    

functions:

```
In [1]: def add(spam, eggs):
   ...:     return spam + eggs
   ...:

In [3]: import dis

In [4]: dis.dis(add)
 2           0 LOAD_FAST                0 (spam)
             3 LOAD_FAST                1 (eggs)
             6 BINARY_ADD
             7 RETURN_VALUE   
```

classes:

```
In [5]: class Parrot:
   ...:     def __init__(self):
   ...:         self.kind = "Norwegian Blue"
   ...:     def is_dead(self):
   ...:         return True
   ...:

In [6]: dis.dis(Parrot)
Disassembly of __init__:
  3           0 LOAD_CONST               1 ('Norwegian Blue')
              3 LOAD_FAST                0 (self)
              6 STORE_ATTR               0 (kind)
              9 LOAD_CONST               0 (None)
             12 RETURN_VALUE

Disassembly of is_dead:
  5           0 LOAD_GLOBAL              0 (True)
              3 RETURN_VALUE
```

code string (3.2+)                             

```
>>> import dis
>>> dis.dis("spam, eggs = 'spam', 'eggs'")
  1           0 LOAD_CONST               3 (('spam', 'eggs'))
              3 UNPACK_SEQUENCE          2
              6 STORE_NAME               0 (spam)
              9 STORE_NAME               1 (eggs)
             12 LOAD_CONST               2 (None)
             15 RETURN_VALUE
```

module

```
$ python -m dis knights.py
  1           0 LOAD_CONST               0 ('print("Ni!")')
              3 STORE_NAME               0 (__doc__)
              6 LOAD_CONST               1 (None)
              9 RETURN_VALUE
```                           

```
# knights.py                                                                 X
print("Ni!")
def is_fresh_wound():
   return True

>>> import knights
Ni!
>>> import dis
>>> dis.dis(knights)
Disassembly of is_fresh_wound:
  3           0 LOAD_GLOBAL              0 (True)
              3 RETURN_VALUE
```              

last traceback

```
>>> print(spam)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'spam' is not defined
>>> import dis
>>> dis.dis()
  1           0 LOAD_NAME                0 (print)
    -->       3 LOAD_NAME                1 (spam)
              6 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
              9 PRINT_EXPR
             10 LOAD_CONST               0 (None)
             13 RETURN_VALUE
```

### Why do we care

debugging

```
>>> ham/eggs + ham/spam
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'ham' is not defined
>>> dis.dis()
  1 -->       0 LOAD_NAME                0 (ham)
              3 LOAD_NAME                1 (eggs)
              6 BINARY_TRUE_DIVIDE
              7 LOAD_NAME                0 (ham)
             10 LOAD_NAME                2 (spam)
             13 BINARY_TRUE_DIVIDE
             14 BINARY_ADD
             15 PRINT_EXPR
             16 LOAD_CONST               0 (None)
             19 RETURN_VALUE
```

### Solving puzzles

outside_fn.py             

```
>>> outside = open('outside_fn.py').read()
>>> import dis
>>> dis.dis(outside)
  1           0 SETUP_LOOP              24 (to 27)
              3 LOAD_NAME                0 (range)
              6 LOAD_CONST               3 (100000000)
              9 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             12 GET_ITER
        >>   13 FOR_ITER                10 (to 26)
             16 STORE_NAME               1 (i)

  2          19 LOAD_NAME                1 (i)
             22 POP_TOP
             23 JUMP_ABSOLUTE           13
        >>   26 POP_BLOCK
        >>   27 LOAD_CONST               2 (None)
             30 RETURN_VALUE
```

inside_fn.py

```
>>> import dis
>>> inside = open('inside_fn.py').read()
>>> dis.dis(inside)
  1           0 LOAD_CONST               0 (<code object run_loop at 0x010F6DE0, file "<dis>", line 1>)
              3 LOAD_CONST               1 ('run_loop')
              6 MAKE_FUNCTION            0
              9 STORE_NAME               0 (run_loop)

  4          12 LOAD_NAME                0 (run_loop)
             15 CALL_FUNCTION            0 (0 positional, 0 keyword pair)
             18 POP_TOP
             19 LOAD_CONST               2 (None)
             22 RETURN_VALUE
```

### Investigate the bytecode diff

STORE_NAME(namei)                          

Implement name = TOS. namei is the index of name in the attribute co_names of
the code object.

LOAD_NAME(namei)

Pushes the value associated with co_names[namei] onto the stack.

STORE_FAST(var_num)

Stores TOS into the local co_varnames[var_num]

LOAD_FAST(var_num)

Push a reference to the local co_varnames[var_num] onto the stack

### Digg the deep

ceval.c: the heart of the beast

A.Kaptur: "A 1500 (!!) line switch statement powers your Python"
(http:aksptur.com/talks/)

- LOAD_FAST (#11368) is ~10 lines, invovles fast locals lookup
- LOAD_NAME (#12353) is ~50 lines, invovles slow dict lookup
- prediction (#11000) makes FOR_ITER + STORE_FAST even faster
