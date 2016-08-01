layout: post
title: "About RCU Read Copy Update"
date: 2015-03-24 17:25:00
tags:
- Mutex
- Lock
categories:
- 内核
---

### Overview

在操作系统中，RCU（Read-Copy-Update）是一种“排它锁”（mutual exclusion/Mutex）的一种
底层的实现，有时也被用来模拟“读写锁”（Reader-Writer-Lock）。它的特点是极低的锁开销和
不用等待的“读操作”。但是，由于RCU的Update操作可能消耗的资源较多，原因是系统必须保留数
据结构的老的版本用来服务之前存在的“读操作”。这些在更新之前的较老的数据结构只能够在所有
之前存在的“读操作”完成之后才能够被系统回收。

在2008年之前，Linux的kernel中大概有2000处使用RCU的API，使用的地方包括网络协议栈和内
存管理系统。截至到2014年3月，大概有9000处使用RCU的API。

<!--more-->

### Advantage and Disadvantage

由于RCU中的数据结构在“所有的读操作完成”之后才会被回收，所以这种特性使RCU的读操作尽可
能使用轻量级的同步，甚至在一些特殊的情境下，根本不需要任何同步。RCU中的更新（Update）
操作利用了大部分操作系统中“单对齐指针（Single Aligned Pointer）的写操作是原子性
（Atomic）的”，允许对一个链表的数据结构进行原子操作（包括插入、删除、替换）的时候不用
关心正在并发进行的读操作。这些并发的读操作可以持续访问数据的老的版本，并且能够在读的同
时，执行原子性的Read-Modify-Write操作，或者其他一些在现代的SMP架构的计算机中一些比较
费资源的“内存屏障（memory barrier）”以及“缓存没有命中（cache misses）”等操作也可以
在没有锁竞争（lock contention）的前提下被同时执行。

相比较而言，在一些传统的“基于锁”的方案里，因为担心在*读操作*的时候，数据结构被更新，而
必须使用“重量级”的锁去阻止任何其他针对该数据结构的操作。

但是RCU也有一些劣势，由于Update操作比较浪费资源，因此经常被应用在一些读比较多而写比较
少的地方。虽然RCU的本质是轻量级的读写同步方案，但是在某些算法里，读写同步并不能有效地
被利用。

### RCU interface

RCU存在于多数的操作系统中，在2002年被加入linux kernel，User级别的实现可以在liburcu中
发现。以下的实现是参考linux kernel 2.6中的RCU实现，核心的API是非常小的：

- rcu_read_lock(): 保证RCU的读操作被“critical section”所保护而不会被系统回收；
- rcu_read_unlock(): 读操作完成之后，通知系统被RCU reader所保护的数据结构可以被系统
回收；
- synchronize_rcu(): 阻塞程序运行止到所有的“之前的”读操作被完成之后，但是该语句并不
阻塞“之后的”的读操作；
    * 由于synchronize_rcu()用来标识所有之前的读操作是否完成，因此它的实现对RCU来说非
    常关键，在一个有大量读操作的环境中，synchronize_rcu的overhead必须要保证非常小。
    在linux中，没有使用阻塞的方法，而是注册一个call back函数用来当所有的之前的读操作
    完成之后被调用。这种call back的变种RCU被linux称作call_rcu；
- rcu_assign_pointer(): RCU的更新操作使用该函数去RCU保护的指针赋一个新的值，用来保证
“updater”与“reader”之间可以安全交流。该函数返回一个新的值，同时也执行其他CPU平台上的
“内存屏障”相关的指令；
- rcu_dereference(): “reader”用来取得一个被RCU保护的指针，该函数返回的值可以安全地被
“dereference”，同时也将执行一些其他辅助的CPU或者平台相关的指令；

![RCU API communication](/images/Rcu_api.jpg)




（待续……）
