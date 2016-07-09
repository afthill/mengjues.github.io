title: Python编程样式指导
date: 2016-01-06 10:07:38
tags:
- Python Style
- Python Coding Guide
categories:
- Python

---

项目地址：<https://github.com/amontalenti/elements-of-python-style>

推荐一下，感觉很靠谱，主要包含了以下建议：

- 推荐使用大多数的PEP8规定的样式
    - 使用flake8工具的校验

- 使用弹性的列长度
    - pep8规定了79个字符，但是偶尔可以使用超过80个字符，并使用`# noqa`避开flake8

- 使用一致的命名习惯
    - 优先使用的命名规则：
        - class name: `CamelCase`，如果有简写需要全部大写`HTTPWrite`
        - varialbe name: `lower_with_underscore`
        - method/function name: `lower_with_underscore`
        - module: `lower_with_underscore.py`，但是优先不需要下划线的名字
        - constant: `UPPER_WITH_UNDERSCORE`
        - re: `name_re`

    - 避免使用命名装饰
        - `_prefix` `suffix_`应该避免
        - `__dunder__`绝大多数时候不要使用

    - 不要使用单字符变量，但以下例外
        - lambda: `encode = lambda x: x.encode('utf-8', 'ignore')`
        - unpacking: `_, url, urlref = data`
        - list/dict/set comprehension: sum(x for x in items if x > 0)

    - 使用self和类似命名规范
        - method: self
        - class: cls
        - `*args` `**kwargs`

- 总是需要保留的良好习惯
    - class总是继承object：
    - 不要放置实例变量在类中
    - 优先使用list/set/dict comprehension，而不是map/filter
    - 使用括号`()`作为断行的连接符
    - 使用括号`()`用来连接fluent API
    - 使用方法调用时隐式的断行连接符（因为外面有一个大括号）
    - 使用isinstance而不是`type ==`
    - 使用with（context manager）在file和lock中
    - 使用`is`来比较None
    - 避免`sys.path` hack
    - 尽量不要创建自己的Exception类
    - 短的docstring应该使用`one line`句子
    - 使用reSt在docstring中
    - 去掉末尾的空格
    - 写出来良好的docstring
        - `good_names + explicit_defaults > verbose_docs + type_specs`

- 范式与模式
    - 关于使用类与使用函数的比较
        - 总是优先使用函数
            - 函数是python的最基本的代码重用的单位
            - 函数具有更好的弹性
        - 类一般使用在如下场景：
            - container
            - proxies
            - type system
        - 当需要把函数放在同一个类中的时候，大部分时间实际需要的是一个module
        - 类可以提供一个`mini namespce`，但是类的主要作用是对对象的内部行为，而不是用来提供group或者命名空间的功能
    - 关于声明式与命令式编程习惯的比较
        - 总是使用声明式编程
            ```
            filtered = []
            for x in items:
                if x.endswith(".py"):
                    filtered.append(x)
            return filtered
            ```

            should be rewritten as:

            ```
            return [x
                    for x in items
                    if x.endswith(".py")]
            ```                
    - 喜欢并掌握generator
    - 优先使用纯粹的function和generator
    - 优先使用简单的arguments和return值
    - 避免传统OOP
    - Mixin多继承有时候是好的，比如django在CBV（class based view）中的使用
    - 对框架使用要谨慎
    - 尊重Python中的元编程
        - decorator
        - context manager
        - descriptor
        - import hook
        - metaclass
        - AST transformation
    - 不用害怕带有双下划线的方法

- 代码之禅
    - 美好大于丑陋
    - 扁平大于嵌套
    - 保持可读性，不要害怕增加`#`注释
    - Error永远都不要被silently抛弃
    - 如果这个实现很难用语言去表达，说明不是一个好的实现
    - 总是喜欢写测试代码

- 选择使用
    - str.format vs over loaded `%`
    - `if item` vs `if item is not None`
    - implicit multiline string vs `"""`
    - raise class or raise class instance

- 标准库和项目结构
    - import datetime as dt
    - dt.datetime.utcnow():优先使用`now()`，因为返回的是本地时间
    - import json，for data interchange
    - from collections import namedtuple，lightweight data types
    - from collections import defaultdict，counting and grouping
    - from collections import deque, fast double ended queue
    - from itertools import groupby, chain: declarative style
    - from functions import wrap: well behaviour decorators
    - argparse: rebust CLI
    - fileinput: create quick UNIX pipe-friendly tools
    - `logging.getLogger(__name__)`: good enough for logging
    - from `__future__ import absolute_import`: fix import aliasing

- 比较好的第三方库:
    - python-dateutil: date and calendar
    - pytz: python timezone
    - tldextract: better URL handling
    - msgpack-python: more compressed encoding than JSON
    - futures: concurrencies utilities
    - docopt: quick throwaway CLI tool
    - py.test: best test runner

- 本地开发项目的基本结构：
    - no `__init__` in root foler
    - `mypackage/__init__.py`优先为`src/mypackage/__init__.py`
    - `mypackage/lib/__init__.py` preferred to `lib/__init__.py`
    - `mypackage/settings.py` preferred to `settings.py`
    - README.rst describes the repo for a newcomer; use reST
    - setup.py for simple facilities like setup.py develop
    - requirements.txt describes package dependencies for pip
    - dev-requirements.txt additional dependencies for tests/local
    - Makefile for simple (!!!) build/lint/test/run steps

<<--end-->>    
