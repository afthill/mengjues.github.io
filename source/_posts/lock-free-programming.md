title: "Fear and loathing in lock free programming"
date: 2017-10-13
categories:
- 内核
---

**About CAS/TAS:**

Several of the most popular CPU architectures have instructions that let you atomically set memory to a new value conditionally if you know the current value. This is called “test and set” (TAS), “compare and swap”, or “compare and set” (CAS). The hardware makes sure that only one thread “wins” if several threads attempt a CAS at the same time. All others are unsuccessful. The return value varies across implementations, but a good implementation clearly indicates success or failure.

假设`CAS(variable, old, new)`是我们所使用的CAS函数，如果运行时我们的`variable`的值为`0`，那么在当前线程中的CAS函数将返回为真，其他所有竞争的线程将返回失败。

CAS可以操作的数据大小一般跟系统的“word size”等同，这个“word size”一般是系统的内存地址（memory address）的长度。比如对于x86_64，CAS一般可以操作64位的数据，但是实际上x86_64可以操作128位的数据，虽然大部分x86_64上的编程语言（Java、Go、Rust）都没有使用128位的CAS。有些特别的系统会设计double word的CAS（DCAS），比如32位的架构，可以在CAS上使用64位的数据，但是大部分不会使用这个技术。

**About Spinlock:**

回形针锁并不是lock free的算法，因为线程将会一直阻塞直到获取锁。示例：

```
import "sync/atomic"

var (
  atomicLock = int32(0)
  locked = int32(1)
  unlocked = int32(0)
)

func lock() {
  pointer := &atomicLock
  old := unlocked
  new := locked
  for {
    if atomic.CompareAndSwapInt32(pointer, old, new) {
      return
    }
  }
}

func unlock() {
  atomic.StoreInt32(&atomicLock, unlocked);
}

func main() {
  lock()
  defer unlock()
}
```

这里的回形针锁（spin lock）在这里跑了一个死循环，不停的使用cas去尝试“原子性地改变atomicLock变量的值，从unlocked到locked”。CPU将会保证只有一个线程可以成功修改atomicLock的值，其他的所有线程讲会一直阻塞线程直到atomicLock的值被改为了unlocked状态。

这里的spinlock看似一直在一个死循环里占用cpu去尝试直到cas成功，但是实际上有的时候，这个方案比mutex更加有效，是因为传统的mutex方案会把线程在阻塞的时候直接放入sleep状态，这导致了线程从sleep到wake的损耗，导致latency提高。所以，现代的mutex使用的其实是一个mutex和spinlock混合的方案。

**About Hybrid Mutex:**

Hybrid mutexes will attempt to acquire the lock quickly in userspace using atomic operations, without giving away their kernel-scheduled appointment on the CPU core. If the gambit didn’t pan out, the hybrid mutex will put the thread to sleep with a blocking syscall.

Many real-world implementations of spinlocks for x86 will issue a PAUSE instruction while spinning, which improves their efficiency dramatically. In addition to spinlocks avoiding the kernel scheduling overhead of going to sleep and waking up again, we also avoid CPU frequency scaling slowdowns that can happen while blocked on a mutex, so when we enter our critical section we don’t need to pay a start-up tax.

**About spinlock over mutext:**

There are still some cases when you would prefer a straight-up spinlock, like if you know the expected time to acquire the lock is less expensive to you than the overhead of a modern hybrid mutex, or if you want a piece of code to be extremely responsive at the cost of some other system resource like power. But as the expected time to acquire the lock goes up, the balance shifts in favor of the mutex (mutexes in most popular threaded languages use the hybrid approach today).

**About lock free structure: Treiber Stack**

A Treiber stack will be our first true lock-free structure! It looks a lot like a linked list, with a head that points to the tip and nodes that point to the next node. We can push things into a lock-free stack like this:
1. Create a node (effectively the same as a linked-list node)
2. Read the current stack.head and set our node.next to it.
3. CAS the stack’s head from the head to our new node. If it worked, we’re done! If not, GOTO 2.
 
 
