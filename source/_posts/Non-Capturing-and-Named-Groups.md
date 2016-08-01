layout: post
title: "Non-capturing and Named Groups"
date: 2015-05-08 10:48:37
tags:
- RE
categories:
- 程序语言
---

### 渊源：

Perl 5 针对标准的“正则表达式”提供了几个额外的feature，Python在re模块中实现了大部
分，但是对于MetaCharacter的选择上，Perl为了突出当前的pattern使用了扩展的正则表达
式，而选择使用`?`作为一个标志，原因是`?`作为一个“重复次数（repeat times）”的限定
符的前面没有任何东西，这就意味着“Repeat Nothing”，这在标准的正则表达式是会出错的。
如果使用其他的单字符的MetaCharacter比如`&`，则会被那些标准的正则表达式识别为正常
的字符，而不是MetaCharacter控制字符。

<!--more-->

Perl使用的扩展的正则表达式的语法是：

>+ `(?...)`
    + `(?=foo)`  表达的是postive lookahead assertion
    + `(?:foo)`  表达的是non-capturing group

而Python则使用的是：

>+ `(?P...)` P表示是python使用的扩展正则表达式
    * `(?P<name>...)` 是named group
    * `(?P=name)` 是backreference to named group

### Non Capturing Group

有时候你需要把正则表达式的某些项用括号括起来，但是这个括号在正则中是有特殊意义，
就是我们常说的分组（Group）：

```
m = re.match("([abc])+", "abc")
m.groups()
# ('abc', )
```

我们可以看到，括号里的匹配的内容被返回了；如果我们不想捕捉匹配到的内容，而只是想
把这些正则表达式括起来方便阅读，那我们可以使用Non-capturing group：

```
m = re.match("(?:[abc])+", "abc")
m.groups()
# ()
```

我们可以看到返回的值是空，也就意味着，括号里的内容并没有被捕捉。

Non Capture Group跟Capture Group最大的区别就是，如果在一个正则表达式有多个Group，
Non Capture Group不会改变Group的序列号，其他方面是一样的，另外需要提醒的是Non
Capture Group和Capture Group是性能上没有区别。

总结一下Non Capture Group：
- 不能通过group取得capture到的内容
- 不改变原来groups的序列号

### Named Group

Named Group的引入是为了解决正则表达式中大量group必须使用数字序号引用的问题，命名
群组是Capturing Group，只不过命名群组给group安置了一个名字，这个名字是附加的，也
就是说命名群组也可以通过数字序号得到group捕捉到的内容：

```
p = re.compile(r'(?P<word>\b\w+\b)')
m = p.search('((((lots of puncuation))))')
m.group('word')
# 'lots'
m.group(1)
# 'lots'
```

命名群组在处理拥有大量的group的正则表达式的时候，比较好用，因为你彻底不用记住组的
数字序号，而可以直接通过group的名字去引用group捕捉到的内容。

### BackReference

Backreference就是向后引用，在比较长的正则表达式中，如果有多个groups，其中一个
group要匹配的内容与前面的group的内容相同，就可以使用`(...)\1`，意思是引用第一个
group，而在Python中由于引入了named group，所以我们可以使用`?P=name`去引用前面的
group里匹配的内容。比如我们想用正则表达式去发现重复的单词，那么我们可以使用`(\b\
w+)\s+\1`，而在Python中如果使用了命名群组，则可以使用`(?:<word>\b\w+)\s+(?P=word)`

```
p = re.compile(r'(?P<word>\b\w+)\s+(?P=word)')
p.search('Paris in the the spring').group()
# 'the the'
```

### Lookahead Assertion

#### `(?=...)`

- Positive lookahead assertion.
- 这里的assertion的意思跟常见的编程语言里assertion类似，就是要保证匹配引擎在遇到
assertion语句时必须保证为真才能让前面的
group拿到匹配的值；
- 示例：
```
string = "begin:id1:tag:id2:tag:id3:end"
print re.findall(r"(id\d+)(?=:tag)", string)
```
返回的是['id1', 'id2']


#### `(?!...)`

- Negative lookahead assertion.
- 这里的negative类似于编程语言里的`!=`或者`not`
- 示例：
```
string = "begin:id1:tag:id2:tag:id3:end"
print re.findall(r"(id\d+)(?!:tag)", string)
```
返回值是['id3']

### Lookbehind Assertion

Lookbehind与Lookahead的区别就是在于assertion的方向，ahead是匹配的时候，需要满足
右边的assertion为真；而behind在匹配的时候，则是要求左边的assertion为真才能匹配；

#### `(?<=...)`

- Positive lookbehind的意思是里面的`...`为真
- 示例：
```
string = "begin:id1:tag:id2:tag:id3:end"
print re.findall(r"(?<=id\d:)(\b\w*\b)", string)
```
返回的是：['tag', 'tag', 'begin']

#### `(?<!...)`

- Negative lookbehind的意思是里面的`...`为假
- 示例：

```
string = "begin:id1:tag:id2:tag:id3:end"
print re.findall(r"(?<!:tag)(\b\w*\b)", string)
```
返回的值是：['begin']

#### (?(id/name)yes-pattern|no-pattern)

- 这里一个更复杂的lookbehind的示例
- 这里的?(...)，这里的...是前面的group的id或者name（如果使用了named group的话）
- 如果前面的引用的group匹配成功，则后面使用yes-pattern匹配，或者使用no-pattern匹配

### Greedy vs Non Greedy

顺便说说贪婪匹配和非贪婪匹配，一般我们在匹配重复的字符的时候，使用的Meta Character
是`*`或者`?`，其中：

- * 表示0次或者更多次的重复
- ? 表示1次或者更多次的重复

如果我们使用了如下的代码：

```
s = '<html><head><title>Title</title>'
len(s)

print re.match('<.*>', s).span()

print re.match('<.*>', s).group()
```

返回的是：

```
<html><head><title>Title</title>
```

但是我们写`<.*>`这个正则表达式的本意是匹配HTML的标签，但是由于RE默认使用贪婪匹配，
我们字符串里的所有的HTML标签都被匹配了。如果要想只匹配单个HTML标签，我们可以使用
`<.*?>`去抑制RE的贪婪匹配。所以：

```
s = '<html><head><title>Title</title>'
print re.match('<.*?>', s).group()
```

返回的是：

```
<html>
```

### 更复杂的例子：

```
class Template:
    """A string class for supporting $-substitutions."""
    __metaclass__ = _TemplateMetaclass

    delimiter = '$'
    idpattern = r'[_a-z][_a-z0-9]*'

    def __init__(self, template):
        self.template = template


class _TemplateMetaclass(type):
    pattern = r"""
    %(delim)s(?:
      (?P<escaped>%(delim)s) |   # Escape sequence of two delimiters
      (?P<named>%(id)s)      |   # delimiter and a Python identifier
      {(?P<braced>%(id)s)}   |   # delimiter and a braced identifier
      (?P<invalid>)              # Other ill-formed delimiter exprs
    )
    """

    def __init__(cls, name, bases, dct):
        super(_TemplateMetaclass, cls).__init__(name, bases, dct)
        if 'pattern' in dct:
            pattern = cls.pattern
        else:
            pattern = _TemplateMetaclass.pattern % {
                'delim' : _re.escape(cls.delimiter),
                'id'    : cls.idpattern,
                }
        cls.pattern = _re.compile(pattern, _re.IGNORECASE | _re.VERBOSE)
```

这里标准库里string实现的简单的template引擎，我们注意到下面这段：

```
pattern = r"""
%(delim)s(?:
  (?P<escaped>%(delim)s) |   # Escape sequence of two delimiters
  (?P<named>%(id)s)      |   # delimiter and a Python identifier
  {(?P<braced>%(id)s)}   |   # delimiter and a braced identifier
  (?P<invalid>)              # Other ill-formed delimiter exprs
)
"""
```

- 这里的对正则表达式还是用了注释，我们可以在re.compile()中加上VERBOSE参数，就可以
使用注释了。
- `?P<...>`使用的named group
- 外部是一个`(?:...)`的Non Capturing Group
- 对于我们上面的那个示例，最后的pattern为：
```
pattern = "\$(?:(?P<escaped>\$)|(?P<named>[_a-z][_a-z0-9]*)|{(?P<braced>[_a-z][_a-z0-9]*)}|(?P<invalid>))"
```
- 如果我们要匹配的字串是`$$$name1${value2}$`，那返回的值应该是：

```
Match 1
escaped	$
braced	None
named	None
invalid	None
Match 2
escaped	None
braced	None
named	name1
invalid	None
Match 3
escaped	None
braced	value2
named	None
invalid	None
Match 4
escaped	None
braced	None
named	None
invalid	Empty
```

### 参考：
- <http://pythex.org/>

Over.
