title: 对waterflow问题的数学描述和函数编程解决方案
date: 2016-07-29 17:18:00
tags:
- algo
categories:
- functional programming
---

原文： <http://philipnilsson.github.io/Badness10k/posts/2013-10-11-functional-waterflow.html>

### waterflow 问题

假设如下图（1），我们在不同的x轴的每一个点都有一个不同的y轴高度。

|![](http://philipnilsson.github.io/Badness10k/images/waterflow1.jpg)|
|--------------------------------|
|图1|

该图可以用一个数组表示，然后数组里的每一个值表示的Y的高度，而数组的序列号（index）则表示为图中
的X轴。

比如上图可以用数组表示为： [2,5,1,2,3,4,7,7,6]

现在假设从上面（Y轴）向下下雨（如图2），而且雨从来也不用考虑会停，那么上图在不同高度的Y轴之间最
多可以收集多少雨水？

|![](http://philipnilsson.github.io/Badness10k/images/waterflow2.jpg)|
|--------------------------------|
|图2|

另外，我们假设1升为（X轴的单位）X（Y轴的单位），即1x1为1升。现在如图2中，最左边的因为没有东西
挡住，所以雨水会洒出，最右边的也是一样。

### 数学描述与答案

请去上面的原文里查看。另外该作者也有另外几篇关于函数式编程的文章，也非常不错，放在附录里了。

### 附录

- [Algebraic patterns - Monoid](http://philipnilsson.github.io/Badness10k/posts/2016-07-21-functional-patterns-monoid.html)
- [Algebraic patterns - Semigroup](http://philipnilsson.github.io/Badness10k/posts/2016-07-14-functional-patterns-semigroup.html)
- [Algebraic patterns - Identity Element](http://philipnilsson.github.io/Badness10k/posts/2016-06-29-functional-patterns-identity-element.html)

### 最后

最近迷上了函数式编程（functional programming），不知道为什么？！
