layout: post
title: "Java String intern()在Java6和之后版本的区别"
date: 2015-03-16 11:00:54
tags:
- String
- Intern
categories:
- 程序语言

---

### String intern的定义：

>In computer science, string interning is a method of storing only one copy of each distinct string value, which must be immutable. Interning strings makes some string processing tasks more time- or space-efficient at the cost of requiring more time when the string is created or interned. The distinct values are stored in a string intern pool.

<!-- more -->

### Java中的intern

The single copy of each string is called its 'intern' and is typically looked up by a method of the string class, for example String.intern() in Java. All compile-time constant strings in Java are automatically interned using this method.<sup>[[\*]](http://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html)</sup>

### 在Java6和7的区别

在Java6中，所有的String是被放在JVM的PermGen中，所以在Java6中如果大量生成了新的Java
对象，将会因为需要不停的GC而严重的影响JVM的性能；而在Java7之后，String被放在heap中，
可以使用的内存几乎是无限大的，我们在后面将会使用Java VisualJVM去观察这两者区别。

### 环境准备

以下准备只可应用与windows环境，其他类型操作系统是类似的：
+ 下载安装JDK6
+ 下载安装JDK7
+ 设置环境变量JAVA6_HOME和JAVA7_HOME分别指向对应的JDK目录

### 准备的测试脚本

```Java
package intern;

public class InternTest {
    public static void main(String[] args) {
        long i = 0;
        while (true) {
            String s = new String("AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA" + i);
            s.intern();
            i++;
        }
    }
}
```

### 在JDK6中的运行结果

打开Java VisualVM：
`%JAVA6_HOME%\bin\jvisualvm.exe`

使用JDK6编译上面的脚本：
`%JAVA6_HOME%\bin\javac intern\InternTest.java`

使用JDK6运行上面的脚本：
`%JAVA6_HOME%\bin\java intern.InternTest`

然后我们可以观察到大量的GC发生在PermGen中，原因是我们的测试程序大量消耗了PermGen。

![*图1 大量的GC发生在PermGen中*](https://cloud.githubusercontent.com/assets/2742842/6775342/aa30f738-d16a-11e4-8289-b4bd472360e6.png)

![*图2 Heap中也有少量的GC*](https://cloud.githubusercontent.com/assets/2742842/6775345/aa37d90e-d16a-11e4-82ce-258614f6d0ba.png)

### 在JDK7中的运行结果

打开Java VisualVM：
`%JAVA7_HOME%\bin\jvisualvm.exe`

使用JDK7编译上面的脚本：
`%JAVA7_HOME%\bin\javac intern\InternTest.java`

使用JDK7运行上面的脚本：
`%JAVA7_HOME%\bin\java intern.InternTest`

然后我们可以观察到大量的GC发生在Heap中，而PermGen则没有任何GC发生。

![*图3 PermGen中几乎没有GC*](https://cloud.githubusercontent.com/assets/2742842/6775343/aa331fe0-d16a-11e4-9fce-c7d9d59d593a.png)

![*图4 Heap中有大量GC*](https://cloud.githubusercontent.com/assets/2742842/6775344/aa35d2e4-d16a-11e4-9b46-136023d2e41d.png)

### 结论

通过上面的对比，我们明显可以看出String.intern()在Java6与Java6以后的版本有完全不通的
实现，在Java6中大量初始化String对象时，应该选择StringBuilder之类的经过优化的对String
处理技术避免在PermGen层的大量的GC。

### 参考

<sup>[1]</sup>String.intern in Java 6, 7 and 8 – string pooling(http://java-performance.info/string-intern-in-java-6-7-8/)
