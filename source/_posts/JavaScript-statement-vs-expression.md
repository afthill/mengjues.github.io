title: 'JavaScript: statement vs expression'
date: 2016-10-24 13:58:28
tags:
- JavaScript
categories:
- 程序语言
---

今天在查阅[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)的时候，看到一个ES6对arrow function的定义：

> ```
> (param1, param2, …, paramN) => { statements }
> (param1, param2, …, paramN) => expression
>          // equivalent to:  => { return expression; }
> ```

对于expression的认识很模糊，我们常常在写常见的function的时候，似乎对expression
和statement的概念区分的不是很明显。比如这里是expression：
- `3 + 4`
- `foo(7)`
- `'abc'.length`

而如果给他们加上分号（实际上没有分号也可以）或者放进函数体内就变成了statement：
```
function bar() {
  3 + 4;
  foo(7);
  'abc'.length;
}
```

那么到底什么是expression？什么是satement？他们区别是什么？

### expression定义

expression可以生产（produce）数据，这个生产的过程是通过evaluation。也就是说
数据没有马上产生，是需要时才会被evaluated。

### statement定义

statement是用来做事情（do things）。这里的数据会马上产生，直到最终的结果才算
当前的statement被执行完毕。

### expression与statement的区别

大部分expression可以被用作statement，只需要把他们放在statement的位置即可，比如
statement最常出现的位置就是函数体内（function body），如下：

```
function bar() {
  3 + 4;
}
```

这里的expression`3+4`因为处于函数体内，被当成了statement。

### 例外

这里是一个function body（block）：
```
const f = x => {bar: 123};
```

而这里是一个expression，返回了object literal：
```
const f = x => ({bar: 123});
```

### 另外一个区别

如果一个expression被当成了function body，那么不需要大括号：
```
asyncFunc.then(x => console.log(x));
```

如果是statement，则需要：
```
asyncFunc.then(x => {throw x});

### 简单总结

expression一般是放在小括号里的`(expression)`，而statement一般是放在大括号里的`{statement}`。