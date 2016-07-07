title: 想去获取或者监控一个process的stdout？
date: 2015-12-23 09:58:33
tags:
- Subprocess
categories:
- Python

---

之前在pyqt中实现监控一个process的stdout的时候，使用python内建库subprocess时，吃足了口头。这个基于pstrace的实现，看起来是好的，不知道具体的效果怎么样，待有空验证。

### 项目地址：

<https://github.com/dellis23/ispy>

### 实现技术栈：

- Python
- *nix pstrace
