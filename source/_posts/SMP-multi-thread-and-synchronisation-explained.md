title: 'SMP: multi-thread and synchronisation explained'
date: 2016-10-24 16:34:51
tags:
- multithreading
categories:
- 内核
---

原文： https://techtake.info/2016/10/13/symmetric-multi-processing-multi-threading-and-synchronisation-explained/

> The operating system assigns a thread to be executed on a core for some time, and then uses the same core for another thread. This process is called scheduling, and the duration for which a thread is assigned to a processor / core is called a time slice. When the time slice for a thread is over, it is <u>pre-empted</u> (taken off the processor) and put aside for scheduling. Whenever the thread gets another time slice for itself, it resumes from where it had been pre-empted in the last run.

这个preempt之前一直一个很深的概念，感觉这个解释很好。`preempt`英文词典中意思
是`先占、先发制人`，这里的主要意思其实是`yield`，就是主动从处理器（processor）
中退出（taken off）。