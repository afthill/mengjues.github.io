title: Great codes in Python (Keep updating)
date: 2016-08-18 10:29:28
tags:
- Python
categories:
- 推荐
---

### [thefuck](https://github.com/nvbn/thefuck)

评审意见： 让人惊喜的是，这个代码库写的可读性很强，并且经过了良好测试。

### [Paramid](https://github.com/Pylons/pyramid/)

评审意见： 这个网站框架的作者与reddit所使用的pylons是同一个作者，每一个Paramid的版本发布都通过单元测试和集成测试保证了100%的语句（statement）覆盖率，这个覆盖率的测试是通过在PyPi上的开源的覆盖率测试工具。另外它还在PyPi上的instrumentation tool上测试工具下保证了95%的分支（decision/condition）覆盖率。

### [cherrypy](http://www.cherrypy.org/)

评审意见： 自从Pylons停止进一步开发后，我发现cherrypy，它的文档跟过去比已经大幅提高。

### [request](https://github.com/kennethreitz/requests)

评审意见： requests偶尔会遇到扩展的问题，因为它的response对象是用3层的。

### [flask](https://github.com/pallets/flask)

评审意见： 我认为Flask引入了非常坏的习惯，就是通过依赖注射（DI）注入的全局的request对象。但是Flask在你的app只需要3个文件，并且每个文件不用超过200行的时候非常好用。

### [Radicale](https://github.com/Kozea/Radicale)

### [web2py]()

### [discord.py](https://github.com/Rapptz/discord.py)

### [Howdoi](https://github.com/gleitz/howdoi)

### [Diamond](https://github.com/python-diamond/Diamond)

### [Werkzeug](https://github.com/mitsuhiko/werkzeug)

### [Tablib](https://github.com/kennethreitz/tablib)

## References:

- http://docs.python-guide.org/en/latest/writing/reading/
- https://www.reddit.com/r/Python/comments/4y39cc/what_the_most_well_designed_written_and_tested/
