title: 使用Python创建命令行中可交互的菜单
date: 2016-01-18 13:32:24
tags:
- Menu
categories:
- Python

---

项目地址： <https://github.com/wong2/pick>

![](https://raw.githubusercontent.com/wong2/pick/master/example/basic.gif)

```
>>> from pick import pick

>>> title = 'Please choose your favorite programming language: '
>>> options = ['Java', 'JavaScript', 'Python', 'PHP', 'C++', 'Erlang', 'Haskell']
>>> option, index = pick(options, title)
```
