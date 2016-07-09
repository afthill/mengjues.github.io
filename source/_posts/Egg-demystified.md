layout: post
title: "Egg Demystified"
date: 2015-07-31 11:21:28
tags:
- Egg
categories:
- Python
---

### Egg的定义：

Egg是Python中模块（module）的发布格式，类似于Java中Jar/War，或者Ruby中的gems。在最近发布
的[PEP427](https://www.python.org/dev/peps/pep-0427/)中，Python对模块的封装提供了一种新的格式wheel。wheel对模块的封装更加
彻底，但是wheel是一种发布包（distribution format），而egg除了可以作为发布包之外，还可以直接安装在sys.path中。

### Egg的内容：

Egg由于可以直接放在sys.path中安装使用，所以egg的发布形式是自我发现的。所谓的自我发现是因为，egg的发布包中包含了meta信息，
这些meta信息包括：
- 该egg包可以提供的功能
- 该egg包需要依赖的外部环境

由于egg包中meta信息的存在，egg安装包可以提供多版本的支持，比如你可以安装几个不同版本的pip包在python的sys.path中，是运行时
使用的时候，可以通过控制sys.path去调用需要的pip版本。这种多版本共存的分离与virtualenv的实现是不同的，virtualenv提供的是独
立的运行环境，而这里是直接安装了多版本的发布包。

### Egg的分类：

Egg在具体发布的时候是非常灵活的，包括以下的形式：
- built eggs
  - built egg是指那些发布时使用目录形式或者zip形式，同时结尾必须是.egg，在zip或者目录的下面有一个子目录叫`EGG-INFO`
    - 比如`pyreadline-2.0-py2.7-win32.egg`是一个zip文件，里面有一个`EGG-INFO`的子目录
    - 或者`certifi-14.05.14-py2.7.egg`是一个目录，同样里面也有一个`EGG-INFO`的子目录
- development eggs
  - development egg是那些正常的目录，但是在正常的目录下面可能分布着一个或者多个的`ProjectName.egg-info`，其中`ProjectName.egg-info`
  中的`.egg-info`必须存在
  - development egg一般用来在没有指定版本号时提供默认的发布包
- egg links
  - 是一种提供跳转的链接文件，以`*.egg-link`为结尾，文件的内容是要跳转到的developement egg
  - 一般用在某些操作系统不提供内生的符号链接(native symbol link)的场合

### pkg_resource的作用：

用来内探或者发掘python环境中安装的模块，然后根据指定的版本号找到需要模块。

### 参考：

1. <https://www.python.org/dev/peps/pep-0427/>
2. <https://mail.python.org/pipermail/distutils-sig/2005-June/004652.html>
