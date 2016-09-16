title: How to show colorful output in Python
date: 2016-09-17 01:13:04
tags:
- Python
categories:
- 程序语言
---

### 第一种方案：

```
class styles:
    HEADER = '\033[95m'
    BLUE = '\033[94m'
    GREEN = '\033[92m'
    WARNING = '\033[93m'
    FAIL = '\033[91m'
    BOLD = '\033[1m'
    UNDERLINE = '\033[4m'
    ENDC = '\033[0m'
```        

### 第二种方案：

colorama: (https://pypi.python.org/pypi/colorama)，跨平台的。

### 第三种方案：

其他库：如termcolor或者blessings。