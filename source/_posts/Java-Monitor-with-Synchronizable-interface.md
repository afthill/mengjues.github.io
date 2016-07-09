title: 如果可以重新设计Java的Synchronizable接口？
date: 2016-01-13 10:15:12
tags:
- 同步
- 并发
categories:
- Java

---

原文：<http://blog.jooq.org/2016/01/12/if-java-were-designed-today-the-synchronizable-interface/>，主要表达了对Java中“synchronize”机制设计时的缺陷进行分析，然后提出了自己的设计建议，大概内容如下：

Java中最令人遗憾的设计之一就是为了简化开发人员在处理多进程竞争时的同步机制而引入了一个概念，每一个Java的对象，都自动拥有一个[monitor](http://stackoverflow.com/q/1648551/521799)。这个设计的缺陷在Java 5的新的并发API（JUCL）被修正，引入了`java.util.concurrent.locks.Lock`类型和一系列子类型。

```
public synchronized void method() {

}
```

```
public void method() {
    synchronized (this) {

    }
}
```

```
public static synchronized void method() {

}
public static void method() {
    synchronized(ClassOfMethod.class) {

    }
}
```

上面几组代码在处理同步机制时，自动使用了Java Object的monitor模式，但是这里的问题是，大部分时间，我们需要处理同步的scope并不是需要如此的coarse-grained（粗粒度）的使用。

除此之外，monitor由于存在于object或者class级别，任何调用者随时都可以使用object或者class上的monitor进行同步操作，这违反了原本设计的封装机制（因为moinitor泄露给了外部调用者）。所以很多人通过Java 5的API设计了私有的Lock对象去避免封装泄露：

```
class SomeClass {
    private Object LOCK = new Object();

    public void method() {
        ...
        synchronized (LOCK) {
            ...
        }
        ...
    }
}
```

## 如果现在重新设计Java的同步机制？

首先应该避免的是，我们不应该允许把同步机制放在任意的对象上，比如：

```
// 错误
synchronized ("abc") {

}
```

这样做是毫无意义的，我们应该只允许锁定可同步的对象（Synchronizable）：

```
Synchronized lock = ...
synchronized (lock) {

}
```

类似于Java中foreach或者`try/with`机制：

```
Iterable<Object> iterable = ...

for(Object o : iterable) {
    ...
}

try (AutoClosable closable = ...) {

}

Runnable runnable = () -> {};
```
