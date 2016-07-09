layout: post
title: "Basic AWK"
date: 2015-04-16 11:15:28
tags:
- AWK
- Text Processing
categories:
- 脚本
------

### About AWK

由Alfred Aho, Peter Weinberger, and Brian Kernighan发明，专门用来做“文本处理”的解释 型语言。awk的最基本的功能就是在文本文件中按照制定的pattern**按行**搜索， awk在行级别 上完成指定的行为，awk持续处理输入的行直到文件的终端。

awk与其他的编程语言有很大的不同，因为awk是数据驱动的（经常需要在awk中描述你想要处理的 数据，然后在数据里找到你想要的结果），大部分其他的语言是过程的（Procedural），你必须 详细描述程序中的每一步需要怎么工作。

awk编程的时候，首先你需要定义一系列规则（rules），每一个rule就是一个模式（pattern） 和一个行为（action）。模式主要是用来指定“要搜索什么”，行为用来指定找到之后的操作。

### awk的语法

一个rule = 一个pattern后面紧跟一个action，action需要用括号，rule用断行分割

```
pattern { action }
pattern { action }
```

### 如何运行awk

```
awk 'program' input-file1 input-file2 ...
```

如何包含awk的program太长，可以把awk放在文件中运行

```
awk -f 'program-file' input-file1 input-file2 ...
```

这里用单引号`'`包围program是为了让awk程序在被shell调用时，不会把awk program中的特殊 字符当作shell中的特殊字符。另外也可以让shell把program作为单参数传给awk，从而可以让 program写在多行中。

如果awk程序在运行时不带参数，如下：

```
awk 'program'
```

awk将会把program附加到标准输入（standard input），直到使用`ctrl + d`表示end-of-file 为止。

#### self-contained awk script

这种awk脚本的写法是在文件的顶端使用如下格式：

```
#!/usr/bin/awk -f
...your program
```

awk是一种解释型的语言，所以在文件的第一行以`#!`开头，然后放上awk解析器的全路径，后面 跟的是一个可选的参数。然后在外部执行这个文件的时候（需要先用`chmod +x yourfile.awk`），操作系统会先寻找解析器，然后使用找到的解析器+参数列表运行后面的程序（program）。

参数列表： - 第一个参数是awk program的全路径（full path） - 后面的参数是awk提供的，或者是inputfiles等

在`#!/usr/bin/awk`后面，通常只能跟一个参数，再多的参数会被OS（操作系统）自动忽略。

最后`ARGV[0]`并不是可靠的，在不同的OS中可能返回不同的值。

### shell引号规则

-	被引号括住的项目，可以自由拼接，最终被shell合并为一个
-	在单个的字符前加`\`表示该字符将会被引号当作纯字符，而不会被shell解释为特殊字符
-	单引号`'`将会屏蔽任何放在其中的字符，这些字符将会逐个被传给shell而不会被解释为特殊 字符
-	双引号“"”屏蔽期间的大部分字符，但是会做变量和命令行的替换

	-	因为双引号中的项目有可能被shell处理，因此，放在双引号中的字符如果跟shell中的特 殊一样，需要手动的屏蔽（escape）。 比如``$, `, \\, "``必须加反斜杠`\\`进行屏蔽。比如下面代码如果用单引号是：

	```
	awk 'BEGIN { print "Don\\47t Panic!" }'
	```

	如果要用双引号就是：

	```
	awk "BEGIN { print \"Don't Panic\"}"
	```

-	空字串（null string）将会被删除，如果跟非空字串放在一起

	-	`awk -F "" 'program' files`
	-	`awk -F"" 'program' files`
	-	第二种形式是错的，双引号将会被删掉，然后program错误地变成了awk的参数

### 如何避免但双引号的混用

-	使用反斜杠`\\`屏蔽
-	使用八进位的屏蔽符，比如`awk 'BEGIN { print "Here is a double quote <\42>" }'`
-	使用命令行变量替换`awk -v sq="'" 'BEGIN { print "Here is a single quote <" sq ">" }'`

### 什么时候使用awk

-	处理元数据（raw data），然后生成报告的时候
-	需要更小体积的程序（program），awk通常是最小的

### awk解析器的命令行参数

-	设置域分隔符（field separator）
	-	`-F fs`
	-	`--field-separator fs`
-	设置awk程序的源文件
	-	`-f source-file`
	-	`--file source-file`
-	命令行传入的临时变量的赋值
	-	`-v var=val`
		-	`- v可以使用多次，传入多个变量`
	-	`--assign var=val`
-	W gawk-opt，提供一些awk参考实现里的额外的命令行参数
-	`--`表明命令行的尾端，后面的命令行参数将会被忽略
-	设置使用单字节字符
	-	`-b`
	-	`--characters-as-bytes`
-	使用GNU兼容模式的awk
	-	`-c`
	-	`--traditional`
-	查看版权信息
	-	`-C`
	-	`--copyright`
-	查看全局变量，类型和最终的值
	-	`-d[file]`，中间没有空格
	-	`--dump-variables[=file]`
-	调试
	-	`-D[file]`
	-	`--debug[=file]`
-	混合命令行awk程序和文件awk程序
	-	`-e program-text`
	-	`--source program-text`
-	执行文件awk
	-	`-E file`
	-	`--exec file`
	-	跟`-f`类似，但是该参数差异在于：
		-	代表着命令行参数的终点
		-	命令行变量`var=value`赋值是不允许
-	编译awk程序源代码为可迁移的通用模板文件，通常用于多语言环境
	-	`-g`
	-	`--gen-pot`
-	帮助
	-	`-h`
	-	`--help`
-	导入库文件
	-	`-i source_file`
	-	`include source-file`
	-	库文件只会被导入一次
-	导入扩展程序进入awk
	-	`l ext`
	-	`--load ext`
	-	awk将会扫描AWKLIBPATH去寻找扩展
	-	扩展的初始化的代码为`dl_load()`
	-	跟在代码中使用`@load类似`
-	是否要求平台迁移性
	-	`-L[value]`
	-	`--lint[=value]`
	-	中间没有空格
-	使用高精度浮点数
	-	`-M`
	-	`--bignum`
-	自动翻译16进制和8进制的数字
	-	`-n`
	-	`--non-decimal-data`
-	强制使用本地的数字格式
	-	`-N`
	-	`--use-lc-numeric`
-	使用格式良好的打印
	-	`-o[file]`
	-	`--pretty-print[=file]`
-	优化
	-	`-O`
	-	`--optimize`
-	调优
	-	`-p[file]`
	-	`--profile[=file]`
-	POSIX兼容
	-	`-P`
	-	`--posix`
-	允许内部表达式
	-	`-r`
	-	`--re-interval`
-	使用沙盒运行awk
	-	`-S`
	-	`--sandbox`
-	与老的awk兼容检查
	-	`-t`
	-	`--lint-old`
-	版本
	-	`-V`
	-	`--version`
