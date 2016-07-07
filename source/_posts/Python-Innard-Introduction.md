layout: post
title: "Python Innard: Introduction"
date: 2015-05-14 17:55:00
tags:
- CPython
- Python Implementation
categories:
- Python

---

翻译：《Python的内核揭秘（一）：入门》

一个朋友曾经对我说：“你懂的，对某些人来说，C语言就是一堆扩展自汇编的宏”。这句话
是在数年前（好吧，是在llvm之前）说的，但是这话也让我语塞。Kernighan和Richie真的
可以在看到C语言的代码的时候可以立即解析为汇编代码？或者Tim Berners-Lee在浏览网页
的时候跟你我看到的内容都不同？或者Keanu Reeves在看一个新出炉的乱七八糟的汤的时候，
看到的究竟跟别人有啥差别？或者，严肃的说，他有看到什么玄机吗？好吧，无论如何，我
们回到程序，Guido van Rossum看到的Python跟别人不同吗？

这篇文章是我在研究Python的内核的时候写的一系列文章的开头，我之所以写这些文章是因
为我觉得能够解释清楚一个东西是深刻理解一个东西的最好的方法，我在这里很愿意把我在
读Python代码时的心得写在这里去尝试解释Python中到底发生了什么。在这个系列文章里，
我主要讨论的是CPython、py3k、bytecode以及其他（unladen swallow、Jython、Cython）
Python相关或者类似的代码。当我在这里说Python的时候，主要说的是CPython，运行的平台
是posix OS，比如linux。如果你想知道Python是怎么工作的，你应该读我的文章；如果你想
为CPython贡献代码，你也应该读我的文章。如果有发现任何错误的地方，你可以在后面写下
你的意见。

我在这里会写下所有关于我在读Python源代码时的心得，但是偶尔也会参考一个资料，比如
[这个](http://docs.python.org/py3k/c-api/index.html)或者[另个](http://docs.python.org/py3k/extending/index.html)或者一些[邮件列表](http://tech.blog.aknin.name/2010/05/07/searching-mailing-list-archives-offline/)及[其他](http://tech.blog.aknin.name/2010/05/07/searching-mailing-list-archives-offline/)。我把所有的资料都放在一起，希望你可以
通过订阅方便阅读。在读我的文章之前，我假设你已经知道一些C语言知识、操作系统理论、
一些汇编、或者一些Unix的爱好。如果没有这些知识也没有关系，但是我不保证你可以顺利
地理解。另外，如果你还没有那些为Python开发所需要的工具链，你可以参考[这里](http://tech.blog.aknin.name/2010/04/08/contributing-to-python/)。

首先，我们从一些假设你已经知道的知识开始，但是我认为他们从我理解的角度是重要的。
Python使用虚拟机（VM）去执行代码，你必须要对虚拟机有一个比较好的认识，你可以把
Python的虚拟机当作是JVM，而不是Virtualbox。我发现从字面意义上理解虚拟机会更容易
懂，它一个用软件打造的机器。CPU是一个电子仪器，用来接受输入（比如data、machine
code），有一个状态（register），然后根据状态+输入数据可以得到输出的结果到RAM或者
Bus。Python的虚拟机也是这样一个机器，使用软件打造，接受机器码输入，有一个状态。这
个虚拟机存在于宿主的一个进程中。

让我们从一个具体的代码来对Python虚拟机有一个大概的理解，当你做`$python -c 'print
("Hello, world!")'`的时候，Python的二进制代码被执行了，标准的C代码库被初始化，然
后主程序开始执行（可以参考源代码`./Modules/python.c` `./Modules/main.c: Py_Main`）。
在一些常见的初始化（输入参数解析、环境变量解析等）之后，[./Python/pythonrun.c: Py_Initialize](http://docs.python.org/c-api/init.html#Py_Initialize)被调用。这个
函数是用来build和assemble，然后去运行虚拟机的，它创立了两个重要的数据结构：
`interpreter state`和`thread state`。同时也创建了sys模块和builtins模块。我们在后
面会深入的解释这些概念。

当这些都完成之后，Python接下来的行动是基于它要如何执行代码：
- `-c`option，用来执行一个字符串
- `-m`option，用来执行模块
- 执行一个python文件
- 运行REPL loop，交互模式

对于我们的示例，我们将会执行一个字符串。为了执行这个字符串，`./Python/pythonrun.c:
PyRun_SimpleStringFlags`将会被调用。这个函数创建了`__main__`命名空间，这个命名空
间将会被用来执行我们的字符串。比如你运行`$ python -c 'a=1; print(a)'`，a将会被存
储在这个命名空间里。在命名空间被创建后，字符串就会在这里被执行，或者也可以理解为
评估（evaluate）或者解析（interpreter）。但是，首先你必须把字符串转换为机器可以
执行的东西。





<!--more-->

> 原文：<http://tech.blog.aknin.name/2010/04/02/pythons-innards-introduction/>

Python’s Innards: Introduction

2010/04/02 § 22 Comments

A friend once said to me: "You know, to some people, C is just a bunch of macros that expand to assembly". It’s been years ago (smartasses: it was also before llvm, ok?), but the sentence stuck with me. Do Kernighan and Ritchie really look at a C program and see assembly code? Does Tim Berners-Lee surf the Web any differently than you and me? And what on earth did Keanu Reeves see when he looked at all of that funky green gibberish soup, anyway? No, seriously, what the heck did he see there?! Uhm, back to the program. Anyway, what does Python look like in Guido van Rossum‘s<sup>1</sup> eyes?

This post marks the beginning of what should develop to a series on Python’s internals, I’m writing it since I believe that explaining something is the best way to grok it, and I’d very much like to be able to visualize more of Python’s ‘funky green gibberish soup’ as I read Python code. On the curriculum is mainly CPython, mainly py3k, mainly bytecode evaluation (I’m not a big compilation fan) – but practically everything around executing Python and Python-like code (Unladen Swallow, Jython, Cython, etc) might turn out to be fair game in this series. For the sake of brevity and my sanity, when I say Python, I mean CPython unless noted otherwise. I also assume a POSIX-like OS or (if and where it matters) Linux, unless otherwise noted. You should read this if you want to know how Python works. You should read this if you want to contribute to CPython. You should read this to find all the mistakes I’ll make and snicker at me behind me back or write snide comments. I realize it’s just your particular way to show affection.

I gather I’ll glean pretty much everything I write about from Python’s source or, occasionally, other fine materials (documentation, especially this and that, certain PyCon lectures, searching python-dev, etc). Everything is out there, but I do hope my efforts at putting it all in one place to which you can RSS-subscribe will make your journey easier. I assume the reader knows some C, some OS theory, a bit less than some assembly (any architecture), a bit more than some Python and has reasonable UNIX fitness (i.e., feels comfortable installing something from source). Don’t be afraid if you’re not reasonably conversant in one (or more) of these, but I can’t promise smooth sailing, either. Also, if you don’t have a working toolchain to do Python development, maybe you’d like to head over here and do as it says on the second paragraph (and onwards, as relevant).

Let’s start with something which I assume you already know, but I think is important, at least to the main way I understand… well, everything that I do understand. I look at it as if I’m looking at a machine. It’s easy in Python’s case, since Python relies on a Virtual Machine to do what it does (like many other interpreted languages). Be certain you understand “Virtual Machine” correctly in this context: think more like JVM and less like VirtualBox (very technically, they’re the same, but in the real world we usually differentiate these two kinds of VMs). I find it easiest to understand “Virtual Machine” literally – it’s a machine built from software. Your CPU is just a complex electronic machine which receives all input (machine code, data), it has a state (registers), and based on the input and its state it will output stuff (to RAM or a Bus), right? Well, CPython is a machine built from software components that has a state and processes instructions (different implementations may use rather different instructions). This software machine operates in the process hosting the Python interpreter. Keep this in mind; I like the machine metaphor (as I explain in minute details here).

That said, let’s start with a bird’s eye overview of what happens when you do this: $ python -c 'print("Hello, world!")'. Python’s binary is executed, the standard C library initialization which pretty much any process does happens and then the main function starts executing (see its source, ./Modules/python.c: main, which soon calls ./Modules/main.c: Py_Main). After some mundane initialization stuff (parse arguments, see if environment variables should affect behaviour, assess the situation of the standard streams and act accordingly, etc), ./Python/pythonrun.c: Py_Initialize is called. In many ways, this function is what ‘builds’ and assembles together the pieces needed to run the CPython machine and makes ‘a process’ into ‘a process with a Python interpreter in it’. Among other things, it creates two very important Python data-structures: the interpreter state and thread state. It also creates the built-in module sys and the module which hosts all builtins. At a later post(s) we will cover all these in depth.

With these in place, Python will do one of several things based on how it was executed. Roughly, it will either execute a string (the -c option), execute a module as an executable (the -m option), or execute a file (passed explicitly on the commandline or passed by the kernel when used as an interpreter for a script) or run its REPL loop (this is more a special case of the file to execute being an interactive device). In the case we’re currently following, it will execute a single string, since we invoked it with -c. To execute this single string, ./Python/pythonrun.c: PyRun_SimpleStringFlags is called. This function creates the __main__ namespace, which is ‘where’ our string will be executed (if you run $ python -c 'a=1; print(a)', where is a stored? in this namespace). After the namespace is created, the string is executed in it (or rather, interpreted or evaluated in it). To do that, you must first transform the string into something that machine can work on.

As I said, I’d rather not focus on the innards of Python’s parser/compiler at this time. I’m not a compilation expert, I’m not entirely interested in it, and as far as I know, Python doesn’t have significant Compiler-Fu beyond the basic CS compilation course. We’ll do a (very) fast overview of what goes on here, and may return to it later only to inspect visible CPython behaviour (see the global statement, which is said to affect parsing, for instance). So, the parser/compiler stage of PyRun_SimpleStringFlags goes largely like this: tokenize and create a Concrete Syntax Tree (CST) from the code, transorm the CST into an Abstract Syntax Tree (AST) and finally compile the AST into a code object using ./Python/ast.c: PyAST_FromNode. For now, think of the code object as a binary string of machine code that Python VM’s ‘machinary’ can operate on – so now we’re ready to do interpretation (again, evaluation in Python’s parlance).

We have an (almost) empty __main__, we have a code object, we want to evaluate it. Now what? Now this line: Python/pythonrun.c: run_mod, v = PyEval_EvalCode(co, globals, locals); does the trick. It receives a code object and a namespace for globals and for locals (in this case, both of them will be the newly created __main__ namespace), creates a frame object from these and executes it. You remember previously that I mentioned that Py_Initialize creates a thread state, and that we’ll talk about it later? Well, back to that for a bit: each Python thread is represented by its own thread state, which (among other things) points to the stack of currently executing frames. After the frame object is created and placed at the top of the thread state stack, it (or rather, the byte code pointed by it) is evaluated, opcode by opcode, by means of the (rather lengthy) ./Python/ceval.c: PyEval_EvalFrameEx.

PyEval_EvalFrameEx takes the frame, extracts opcode (and operands, if any, we’ll get to that) after opcode, and executes a short piece of C code matching the opcode. Let’s take a closer look at what these “opcodes” look like by disassembling a bit of compiled Python code:

    >>> from dis import dis # ooh! a handy disassembly function!
    >>> co = compile("spam = eggs - 1", "<string>", "exec")
    >>> dis(co)
      1           0 LOAD_NAME                0 (eggs)
                  3 LOAD_CONST               0 (1)
                  6 BINARY_SUBTRACT
                  7 STORE_NAME               1 (spam)
                 10 LOAD_CONST               1 (None)
                 13 RETURN_VALUE
    >>>

…even without knowing much about Python’s bytecode, this is reasonably readable. You “load” the name eggs (where do you load it from? where do you load it to? soon), and also load a constant value (1), then you do a “binary subtract” (what do you mean ‘binary’ in this context? between which operands?), and so on and so forth. As you might have guessed, the names are “loaded” from the globals and locals namespaces we’ve seen earlier, and they’re loaded onto an operand stack (not to be confused with the stack of running frames), which is exactly where the binary subtract will pop them from, subtract one from the other, and put the result back on that stack. “Binary subtract” just means this is a subtraction opcode that has two operands (hence it is “binary”, this is not to say the operands are binary numbers made of ‘0’s and ‘1’s).

You can go look at PyEval_EvalFrameEx at ./Python/ceval.c yourself, it’s not a small function by any means. For practical reasons I can’t paste too much code from there in here, but I will just paste the code that runs when a BINARY_SUBTRACT opcode is found, I think it really illustrates things:

        TARGET(BINARY_SUBTRACT)
            w = POP();
            v = TOP();
            x = PyNumber_Subtract(v, w);
            Py_DECREF(v);
            Py_DECREF(w);
            SET_TOP(x);
            if (x != NULL) DISPATCH();
            break;

…pop something, take the top (of the operand stack), call a C function called PyNumber_Subtract() on these things, do something we still don’t understand (but will in due time) called “Py_DECREF” on both, set the top of the stack to the result of the subtraction (overwriting the previous top) and then do something else we don’t understand if x is not null, which is to do a “DISPATCH”. So while we have some stuff we don’t understand, I think it’s very apparent how two numbers are subtracted in Python, at the lowest possible level. And it took us just about 1,500 words to reach here, too!

After the frame is executed and PyRun_SimpleStringFlags returns, the main function does some cleanup (notably, Py_Finalize, which we’ll discuss), the standard C library deinitialization stuff is done (atexit, et al), and the process exits.

I hope this gives us a “good enough” overview that we can later use as scaffolding on which, in later posts, we’ll hang the more specific discussions of various areas of Python. We have quite a few terms I promised to get back to: interpreter and thread state, namespaces, modules and builtins, code and frame objects as well as those DECREF and DISPATCH lines we didn’t understand inside BINARY_SUBTRACT‘s implementation. There’s also a very crucial ‘phantom’ term that we’ve danced around all over this article but didn’t call by name – objects. CPython’s object system is central to understanding how it works, and I reckon we’ll cover that – at length – in the next post of this series. Stay tuned.

1 Do note that this series isn’t endorsed nor affiliated with anyone unless explicitly stated. Also, note that though Python was initiated by Guido van Rossum, many people have contributed to it and to derivative projects over the years. I never checked with Guido nor with them, but I’m certain Python is no less clear to many of these contributors’ eyes than it is to Guido’s.
=======
layout: post
title: "Python Innard: Introduction"
date: 2015-05-14 17:55:00
tags:
- CPython
- Python Implementation
categories:
- Python

---

翻译：《Python的内核揭秘（一）：入门》

一个朋友曾经对我说：“你懂的，对某些人来说，C语言就是一堆扩展自汇编的宏”。这句话
是在数年前（好吧，是在llvm之前）说的，但是这话也让我语塞。Kernighan和Richie真的
可以在看到C语言的代码的时候可以立即解析为汇编代码？或者Tim Berners-Lee在浏览网页
的时候跟你我看到的内容都不同？或者Keanu Reeves在看一个新出炉的乱七八糟的汤的时候，
看到的究竟跟别人有啥差别？或者，严肃的说，他有看到什么玄机吗？好吧，无论如何，我
们回到程序，Guido van Rossum看到的Python跟别人不同吗？

这篇文章是我在研究Python的内核的时候写的一系列文章的开头，我之所以写这些文章是因
为我觉得能够解释清楚一个东西是深刻理解一个东西的最好的方法，我在这里很愿意把我在
读Python代码时的心得写在这里去尝试解释Python中到底发生了什么。在这个系列文章里，
我主要讨论的是CPython、py3k、bytecode以及其他（unladen swallow、Jython、Cython）
Python相关或者类似的代码。当我在这里说Python的时候，主要说的是CPython，运行的平台
是posix OS，比如linux。如果你想知道Python是怎么工作的，你应该读我的文章；如果你想
为CPython贡献代码，你也应该读我的文章。如果有发现任何错误的地方，你可以在后面写下
你的意见。

我在这里会写下所有关于我在读Python源代码时的心得，但是偶尔也会参考一个资料，比如
[这个](http://docs.python.org/py3k/c-api/index.html)或者[另个](http://docs.python.org/py3k/extending/index.html)或者一些[邮件列表](http://tech.blog.aknin.name/2010/05/07/searching-mailing-list-archives-offline/)及[其他](http://tech.blog.aknin.name/2010/05/07/searching-mailing-list-archives-offline/)。我把所有的资料都放在一起，希望你可以
通过订阅方便阅读。在读我的文章之前，我假设你已经知道一些C语言知识、操作系统理论、
一些汇编、或者一些Unix的爱好。如果没有这些知识也没有关系，但是我不保证你可以顺利
地理解。另外，如果你还没有那些为Python开发所需要的工具链，你可以参考[这里](http://tech.blog.aknin.name/2010/04/08/contributing-to-python/)。

首先，我们从一些假设你已经知道的知识开始，但是我认为他们从我理解的角度是重要的。
Python使用虚拟机（VM）去执行代码，你必须要对虚拟机有一个比较好的认识，你可以把
Python的虚拟机当作是JVM，而不是Virtualbox。我发现从字面意义上理解虚拟机会更容易
懂，它一个用软件打造的机器。CPU是一个电子仪器，用来接受输入（比如data、machine
code），有一个状态（register），然后根据状态+输入数据可以得到输出的结果到RAM或者
Bus。Python的虚拟机也是这样一个机器，使用软件打造，接受机器码输入，有一个状态。这
个虚拟机存在于宿主的一个进程中。

让我们从一个具体的代码来对Python虚拟机有一个大概的理解，当你做`$python -c 'print
("Hello, world!")'`的时候，Python的二进制代码被执行了，标准的C代码库被初始化，然
后主程序开始执行（可以参考源代码`./Modules/python.c` `./Modules/main.c: Py_Main`）。
在一些常见的初始化（输入参数解析、环境变量解析等）之后，[./Python/pythonrun.c: Py_Initialize](http://docs.python.org/c-api/init.html#Py_Initialize)被调用。这个
函数是用来build和assemble，然后去运行虚拟机的，它创立了两个重要的数据结构：
`interpreter state`和`thread state`。同时也创建了sys模块和builtins模块。我们在后
面会深入的解释这些概念。

当这些都完成之后，Python接下来的行动是基于它要如何执行代码：
- `-c`option，用来执行一个字符串
- `-m`option，用来执行模块
- 执行一个python文件
- 运行REPL loop，交互模式

对于我们的示例，我们将会执行一个字符串。为了执行这个字符串，`./Python/pythonrun.c:
PyRun_SimpleStringFlags`将会被调用。这个函数创建了`__main__`命名空间，这个命名空
间将会被用来执行我们的字符串。比如你运行`$ python -c 'a=1; print(a)'`，a将会被存
储在这个命名空间里。在命名空间被创建后，字符串就会在这里被执行，或者也可以理解为
评估（evaluate）或者解析（interpreter）。但是，首先你必须把字符串转换为机器可以
执行的东西。





<!--more-->

> 原文：<http://tech.blog.aknin.name/2010/04/02/pythons-innards-introduction/>

Python’s Innards: Introduction

2010/04/02 § 22 Comments

A friend once said to me: "You know, to some people, C is just a bunch of macros that expand to assembly". It’s been years ago (smartasses: it was also before llvm, ok?), but the sentence stuck with me. Do Kernighan and Ritchie really look at a C program and see assembly code? Does Tim Berners-Lee surf the Web any differently than you and me? And what on earth did Keanu Reeves see when he looked at all of that funky green gibberish soup, anyway? No, seriously, what the heck did he see there?! Uhm, back to the program. Anyway, what does Python look like in Guido van Rossum‘s<sup>1</sup> eyes?

This post marks the beginning of what should develop to a series on Python’s internals, I’m writing it since I believe that explaining something is the best way to grok it, and I’d very much like to be able to visualize more of Python’s ‘funky green gibberish soup’ as I read Python code. On the curriculum is mainly CPython, mainly py3k, mainly bytecode evaluation (I’m not a big compilation fan) – but practically everything around executing Python and Python-like code (Unladen Swallow, Jython, Cython, etc) might turn out to be fair game in this series. For the sake of brevity and my sanity, when I say Python, I mean CPython unless noted otherwise. I also assume a POSIX-like OS or (if and where it matters) Linux, unless otherwise noted. You should read this if you want to know how Python works. You should read this if you want to contribute to CPython. You should read this to find all the mistakes I’ll make and snicker at me behind me back or write snide comments. I realize it’s just your particular way to show affection.

I gather I’ll glean pretty much everything I write about from Python’s source or, occasionally, other fine materials (documentation, especially this and that, certain PyCon lectures, searching python-dev, etc). Everything is out there, but I do hope my efforts at putting it all in one place to which you can RSS-subscribe will make your journey easier. I assume the reader knows some C, some OS theory, a bit less than some assembly (any architecture), a bit more than some Python and has reasonable UNIX fitness (i.e., feels comfortable installing something from source). Don’t be afraid if you’re not reasonably conversant in one (or more) of these, but I can’t promise smooth sailing, either. Also, if you don’t have a working toolchain to do Python development, maybe you’d like to head over here and do as it says on the second paragraph (and onwards, as relevant).

Let’s start with something which I assume you already know, but I think is important, at least to the main way I understand… well, everything that I do understand. I look at it as if I’m looking at a machine. It’s easy in Python’s case, since Python relies on a Virtual Machine to do what it does (like many other interpreted languages). Be certain you understand “Virtual Machine” correctly in this context: think more like JVM and less like VirtualBox (very technically, they’re the same, but in the real world we usually differentiate these two kinds of VMs). I find it easiest to understand “Virtual Machine” literally – it’s a machine built from software. Your CPU is just a complex electronic machine which receives all input (machine code, data), it has a state (registers), and based on the input and its state it will output stuff (to RAM or a Bus), right? Well, CPython is a machine built from software components that has a state and processes instructions (different implementations may use rather different instructions). This software machine operates in the process hosting the Python interpreter. Keep this in mind; I like the machine metaphor (as I explain in minute details here).

That said, let’s start with a bird’s eye overview of what happens when you do this: $ python -c 'print("Hello, world!")'. Python’s binary is executed, the standard C library initialization which pretty much any process does happens and then the main function starts executing (see its source, ./Modules/python.c: main, which soon calls ./Modules/main.c: Py_Main). After some mundane initialization stuff (parse arguments, see if environment variables should affect behaviour, assess the situation of the standard streams and act accordingly, etc), ./Python/pythonrun.c: Py_Initialize is called. In many ways, this function is what ‘builds’ and assembles together the pieces needed to run the CPython machine and makes ‘a process’ into ‘a process with a Python interpreter in it’. Among other things, it creates two very important Python data-structures: the interpreter state and thread state. It also creates the built-in module sys and the module which hosts all builtins. At a later post(s) we will cover all these in depth.

With these in place, Python will do one of several things based on how it was executed. Roughly, it will either execute a string (the -c option), execute a module as an executable (the -m option), or execute a file (passed explicitly on the commandline or passed by the kernel when used as an interpreter for a script) or run its REPL loop (this is more a special case of the file to execute being an interactive device). In the case we’re currently following, it will execute a single string, since we invoked it with -c. To execute this single string, ./Python/pythonrun.c: PyRun_SimpleStringFlags is called. This function creates the __main__ namespace, which is ‘where’ our string will be executed (if you run $ python -c 'a=1; print(a)', where is a stored? in this namespace). After the namespace is created, the string is executed in it (or rather, interpreted or evaluated in it). To do that, you must first transform the string into something that machine can work on.

As I said, I’d rather not focus on the innards of Python’s parser/compiler at this time. I’m not a compilation expert, I’m not entirely interested in it, and as far as I know, Python doesn’t have significant Compiler-Fu beyond the basic CS compilation course. We’ll do a (very) fast overview of what goes on here, and may return to it later only to inspect visible CPython behaviour (see the global statement, which is said to affect parsing, for instance). So, the parser/compiler stage of PyRun_SimpleStringFlags goes largely like this: tokenize and create a Concrete Syntax Tree (CST) from the code, transorm the CST into an Abstract Syntax Tree (AST) and finally compile the AST into a code object using ./Python/ast.c: PyAST_FromNode. For now, think of the code object as a binary string of machine code that Python VM’s ‘machinary’ can operate on – so now we’re ready to do interpretation (again, evaluation in Python’s parlance).

We have an (almost) empty __main__, we have a code object, we want to evaluate it. Now what? Now this line: Python/pythonrun.c: run_mod, v = PyEval_EvalCode(co, globals, locals); does the trick. It receives a code object and a namespace for globals and for locals (in this case, both of them will be the newly created __main__ namespace), creates a frame object from these and executes it. You remember previously that I mentioned that Py_Initialize creates a thread state, and that we’ll talk about it later? Well, back to that for a bit: each Python thread is represented by its own thread state, which (among other things) points to the stack of currently executing frames. After the frame object is created and placed at the top of the thread state stack, it (or rather, the byte code pointed by it) is evaluated, opcode by opcode, by means of the (rather lengthy) ./Python/ceval.c: PyEval_EvalFrameEx.

PyEval_EvalFrameEx takes the frame, extracts opcode (and operands, if any, we’ll get to that) after opcode, and executes a short piece of C code matching the opcode. Let’s take a closer look at what these “opcodes” look like by disassembling a bit of compiled Python code:

    >>> from dis import dis # ooh! a handy disassembly function!
    >>> co = compile("spam = eggs - 1", "<string>", "exec")
    >>> dis(co)
      1           0 LOAD_NAME                0 (eggs)
                  3 LOAD_CONST               0 (1)
                  6 BINARY_SUBTRACT
                  7 STORE_NAME               1 (spam)
                 10 LOAD_CONST               1 (None)
                 13 RETURN_VALUE
    >>>

…even without knowing much about Python’s bytecode, this is reasonably readable. You “load” the name eggs (where do you load it from? where do you load it to? soon), and also load a constant value (1), then you do a “binary subtract” (what do you mean ‘binary’ in this context? between which operands?), and so on and so forth. As you might have guessed, the names are “loaded” from the globals and locals namespaces we’ve seen earlier, and they’re loaded onto an operand stack (not to be confused with the stack of running frames), which is exactly where the binary subtract will pop them from, subtract one from the other, and put the result back on that stack. “Binary subtract” just means this is a subtraction opcode that has two operands (hence it is “binary”, this is not to say the operands are binary numbers made of ‘0’s and ‘1’s).

You can go look at PyEval_EvalFrameEx at ./Python/ceval.c yourself, it’s not a small function by any means. For practical reasons I can’t paste too much code from there in here, but I will just paste the code that runs when a BINARY_SUBTRACT opcode is found, I think it really illustrates things:

        TARGET(BINARY_SUBTRACT)
            w = POP();
            v = TOP();
            x = PyNumber_Subtract(v, w);
            Py_DECREF(v);
            Py_DECREF(w);
            SET_TOP(x);
            if (x != NULL) DISPATCH();
            break;

…pop something, take the top (of the operand stack), call a C function called PyNumber_Subtract() on these things, do something we still don’t understand (but will in due time) called “Py_DECREF” on both, set the top of the stack to the result of the subtraction (overwriting the previous top) and then do something else we don’t understand if x is not null, which is to do a “DISPATCH”. So while we have some stuff we don’t understand, I think it’s very apparent how two numbers are subtracted in Python, at the lowest possible level. And it took us just about 1,500 words to reach here, too!

After the frame is executed and PyRun_SimpleStringFlags returns, the main function does some cleanup (notably, Py_Finalize, which we’ll discuss), the standard C library deinitialization stuff is done (atexit, et al), and the process exits.

I hope this gives us a “good enough” overview that we can later use as scaffolding on which, in later posts, we’ll hang the more specific discussions of various areas of Python. We have quite a few terms I promised to get back to: interpreter and thread state, namespaces, modules and builtins, code and frame objects as well as those DECREF and DISPATCH lines we didn’t understand inside BINARY_SUBTRACT‘s implementation. There’s also a very crucial ‘phantom’ term that we’ve danced around all over this article but didn’t call by name – objects. CPython’s object system is central to understanding how it works, and I reckon we’ll cover that – at length – in the next post of this series. Stay tuned.

1 Do note that this series isn’t endorsed nor affiliated with anyone unless explicitly stated. Also, note that though Python was initiated by Guido van Rossum, many people have contributed to it and to derivative projects over the years. I never checked with Guido nor with them, but I’m certain Python is no less clear to many of these contributors’ eyes than it is to Guido’s.
