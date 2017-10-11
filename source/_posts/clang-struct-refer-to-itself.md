title: "为什么c语言中的struct引用自己时，必须使用指针？"
date: 2017-10-11
categories:
- 程序语言
---

很多人提到的forward declaration，但是这里的这个人讲了[为什么](https://stackoverflow.com/questions/588623/self-referential-struct-definition)，感觉很有益：

> its very important to note that structures are 'value' types i.e they contain the actual value, so when you declare a structure the compiler has to decide how much memory to allocate to an instance of it, so it goes through all its members and adds up their memory to figure out the over all memory of the struct, but if the compiler found an instance of the same struct inside then this is a paradox (i.e in order to know how much memory struct A takes you have to decide how much memory struct A takes !).

> But reference types are different, if a struct 'A' contains a 'reference' to an instance of its own type, although we don't know yet how much memory is allocated to it, we know how much memory is allocated to a memory address (i.e the reference).
