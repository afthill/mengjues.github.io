title: Remote run shell through python
date: 2016-01-13 10:41:09
tags:
- Remote Shell
categories:
- 程序语言

---

项目地址：<https://github.com/seiferteric/telepythy>

可以通过ssh调用远程机器的shell，大概示例：

```
from telepythy import Remote

h = Remote('localhost')


def getfile(filename="/etc/hosts"):
    return open(filename).read()


try:
    print(h.run(getfile))
    h.close()
except Exception as e:
    print("Handled Exception...")
    print(e)
```    
