title: A pratical introduction to FP
date: 2016-12-26 00:11:43
tags:
- Python
categories:
- 函数编程
---

原文：https://maryrosecook.com/blog/post/a-practical-introduction-to-functional-programming

### 什么是函数式编程的指导性原则

**不是下面这些函数式编程的语言特征**
- immutable data
- first class function
- tail call optimization

**不是下面这些函数式的变成技巧**
- mapping
- reducing
- pipelining
- recursing
- currying
- high order function

**不是下面这些函数式语言带来的优势**
- parallelization
- lazy evalution
- determinism

**只有一种东西可以表达函数式编程的核心特征：absence of side effects。函数式编程不依赖于当前函数的外部的数据，而且也不改变当前函数的外部数据**

**unfunctional function:**   
```
a = 0
def increment():
    global a
    a += 1
```

**functional function:**
```
def increment(a):
    return a + 1
```

几个要注意的简单的原则：
- Don’t iterate over lists. Use map and reduce.
- Write declaratively, not imperatively
- Use pipelines

