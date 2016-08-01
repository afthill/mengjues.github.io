layout: post
title: "Map with None function"
date: 2015-08-03 15:39:28
tags:
- Map
- Zip
categories:
- 程序语言
---

### map函数的签名

首先，我们来看看map函数的签名：

> map                    
> Docstring:                         
> map(function, sequence[, sequence, ...]) -> list

看到他的arg[1]是一个function对象，然后arg[2]后面是一个可变的参数，可以传入无限制的sequence对象，比如tuple、list、set、
dict等。

### 正常的使用场景

```
import operator

map(operator.add, range(10), range(10))
Out[13]: [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

### 不正常的使用场景

我们来看看map的具体的docstring：         
> Docstring:              
> map(function, sequence[, sequence, ...]) -> list              
>            
> Return a list of the results of applying the function to the items of   
> the argument sequence(s).  If more than one sequence is given, the         
> function is called with an argument list consisting of the corresponding        
> item of each sequence, substituting None for missing values when not all            
> sequences have the same length.  **If the function is None, return a list of**              
> **the items of the sequence (or a list of tuples if more than one sequence).**                      
> Type:      builtin_function_or_method                

注意看粗体的部分，如果我们传入的function为None，那么map返回的对象就是一个包含了所有传入的sequence的list，并且如果我们
传入了多个sequence，那么返回的将会是一个包含了tuple的list（a list of tuple）。

```
map(None, range(10), range(10))
Out[14]:
[(0, 0),
 (1, 1),
 (2, 2),
 (3, 3),
 (4, 4),
 (5, 5),
 (6, 6),
 (7, 7),
 (8, 8),
 (9, 9)]
```

如果传入的sequence没有对齐呢，比如sequence1的长度是8，sequence2的长度是10，那么再看看这段文档的上半部分。如果传入了多个
sequence而且传入的sequence是没有对齐的，那么长度较小的sequence将会使用None，补齐。我们看代码：

```
map(None, range(10), range(8))
Out[15]:
[(0, 0),
 (1, 1),
 (2, 2),
 (3, 3),
 (4, 4),
 (5, 5),
 (6, 6),
 (7, 7),
 (8, None),
 (9, None)]
```

如果我们此时用zip，与map的区别是，zip使用最短的sequence，组成最终的list tuple。

```
zip(range(10), range(8))
Out[16]: [(0, 0), (1, 1), (2, 2), (3, 3), (4, 4), (5, 5), (6, 6), (7, 7)]
```

--END--
