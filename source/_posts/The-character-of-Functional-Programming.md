title: The character of Functional Programming
date: 2017-09-14 17:20:53
tags:
- Functional Promgramming
categories:
- 程序语言
---

在看Oreilly的《Functional Programming in Python》这个免费的小书的时候，里面提到了函数式编程的定义，作者说，什么是函数式编程（后面都简称为FP），首先要完全的讲清楚定义就是一个非常难的问题。所以要真正的讲清楚，什么不是FP？

- FP不是你使用了所谓的函数式编程语言（Lisp、Clojure、Scala、Haskell、ML、OCAML、ErLang等）去编写程序就可以叫做FP。虽然这个答案很安全，但是没有解释到底什么是FP。包括很多使用了这些编程语言本身的人也很难解释清楚啥是FP。就比如瞎子摸象一样，每一个人看到的都是自己所知道的局部。
- 拿FP跟命令式编程（imperative programming）对比也是一个安全的答案，但是依然没有回答问题。
- 或者FP不是OOP（object oriented programming），不是答案！
- FP不是Logic Programming（比如prolog）。

所以作者在这里总结的函数式编程要符合下面的特征：

- function是一级类或者对象。也就是说，你对对象的所有的操作，同样也可以被应用到function上。
- recursion被用作基础的控制结果（control structure），甚至在某些FP语言中没有loop这样的控制结构存在。
- 聚焦于list processing（也是lisp语言的由来）。list或者sublist经常与递归一起使用用来代替loops。
- FP拒绝所有的边际效应（side effects）。在命令式编程中，经常使用变量来跟踪state，而在FP中使用的是不可变的数据结构。
- FP拒绝使用statement，而只使用expression。
- FP通常更加关注what而不是how。
- FP大量使用high order function（function嵌套）去解决实际的问题。
