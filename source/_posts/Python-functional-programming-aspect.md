title: Python functional programming aspect
date: 2016-07-11 15:14:39
tags:
- Python
- Functional Programming
categories:
- 技术
---

### 名词解释

- TCO (Tail Call Optimization): 尾调优化
- CPS (Continuous Passing Style): 续传样式

### TCO 库

- tail call optimization
- optimized tail recursion
- continuation passing style
- pseudo call-with-current-continuation

### tco库的主要动机：  

重复的调用function的时候，并不会增加执行栈（execution stack）的大小，并且在函数式编程中可以随时从一个执行栈的某一部分跳转到另外一部分之前的代码，同时并不需要穿越中间的函数调用和return。

#### 第一个实例，二叉搜索树

> 假定我们有一个二叉树，现在我们需要在这个二叉树上搜索制定的节点（node）。

使用尾递归（tail recursion）

```
from itertools import groupby
from random import sample

nbrlevels = 16
n = 2 ** nbrlevels - 1
t = sample(range(2 * n), n)
t.sort()

def make_tree(l):
    if len(l) == 1:
        return [l[0], [], []]

    return [l[0], make_tree(l[1:len(l)//2+1]), make_tree(l[len(l)//2+1:])]

tree = make_tree(t)
```

如果使用tco，则：
- 递归调用自己，且不重载（overloading stack）
- 在递归的内部不退出递归的情况下调用其他的函数

待续---


参考： 
1) <http://baruchel.github.io/python/2015/11/07/explaining-functional-aspects-in-python/>       
2) <https://github.com/baruchel/tco>          
3) <https://en.wikipedia.org/wiki/Continuation-passing_style>       
4) <https://blogs.msdn.microsoft.com/ericlippert/2010/10/21/continuation-passing-style-revisited-part-one/>      
5) <https://www.zhihu.com/question/24453254>           
6) <http://blog.pengyifan.com/articles/functional-programming-for-the-rest-of-us/>       
7) <http://blog.csdn.net/hikaliv/article/details/4548561>      
8) <https://segmentfault.com/n/1330000005012969>          
9) <http://baruchel.github.io/python/2015/07/10/continuation-passing-style-in-python/>            
10) <https://github.com/baruchel/tco>
