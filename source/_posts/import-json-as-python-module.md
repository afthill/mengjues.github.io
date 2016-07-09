title: import json as python module
date: 2016-01-26 09:40:35
tags:
- JSON
categories:
- Fun
---

项目地址：<https://github.com/kragniz/json-sempai>

直接像python的module一样导入json文件？

tester.json

```
{
    "hello": "world",
    "this": {
        "can": {
            "be": "nested"
        }
    }
}
```

```
>>> from jsonsempai import magic
>>> import tester
>>> tester
<module 'tester' from 'tester.json'>
>>> tester.hello
u'world'
>>> tester.this.can.be
u'nested'
>>>
```

非常疯狂和好玩的主意，呵呵。正如作者所说：

> Disclaimer: Only do this if you hate yourself and the rest of the world.
