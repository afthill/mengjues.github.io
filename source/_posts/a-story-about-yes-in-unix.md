title "一个好玩的关于unix中一个非常简单的工具yes的优化实现"
date: 2017-10-12
categories:
- 内核
---

yes这个小工具在*nix中会往stdout无限打印y，我们通常可能认为实现就是写一个死循环，开始的实现的确是这样：

```
main(argc, argv)
char **argv;
{
  for (;;)
    printf("%s\n", argc>1? argv[1]: "y");
}
```

这个是1979年写的程序！

但是这个版本的througput很低，更好玩的优化在[这里](https://matthias-endler.de/2017/yes/)。
