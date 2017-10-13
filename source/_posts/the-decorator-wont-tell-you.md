title: "python decorator解密"
date: 2017-10-13
categories:
- 程序语言
---

源：<https://github.com/hchasestevens/posts/blob/master/notebooks/the-decorators-they-wont-tell-you-about.ipynb>

感觉作者在这里写的这一点很好：
* Decorators are applied once, at function definition time. 这里的意思呢，这个docorator是在“编译”时就被应用，而不是在“运行”时。
* Annotating a function definition x with a decorator `@d` is equivalent to defining `x`, then, immediately afterward, having `x = d(x)`.同样强调，这个decorator在这里类似于静态语言的内联（inline）。
* Decorating a function with `@d` and `@e`, in that order, is equivalent to performing `x = d(e(x))` after the function's definition.这里强调的是decorator在这里使用的是函数编程里的习惯。


