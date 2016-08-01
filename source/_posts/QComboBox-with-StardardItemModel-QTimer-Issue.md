layout: post
title: "复盘一个关于QComboBox的issue"
date: 2015-06-01 12:46:00
tags:
- PyQt
categories:
- 程序语言

---

这个最近遇到的一个小问题，但是花了一天时间找到了[答案](http://stackoverflow.com/questions/19887777/error-in-model-view-implemention-of-gui-in-pyqt)，下面是详细复盘：


### 1. 先看一个最简单的示例：

```
import sys
from PyQt4.QtGui import *
from PyQt4.QtCore import Qt


if __name__ == "__main__":
    app = QApplication(sys.argv)

    from PyQt4.QtGui import QDialog
    mywin = QDialog()
    mywin.setMinimumSize(400, 400)

    model = QStandardItemModel()

    for i, word in enumerate(['hola', 'adios', 'hello', 'good bye']):
        item = QStandardItem(word)
        model.setItem(i, 0, item)

    combo = QComboBox(mywin)
    combo.setModel(model)
    bombo.setEditable(True)
    mywin.show()

    sys.exit(app.exec_())
```

如果在`Python 2.7.9`以及`PyQt 4.11.3`下运行，则一定会出现如下的warning:

```
QObject::startTimer: QTimer can only be used with threads started with QThread

Process finished with exit code 0
```

不是什么大的问题，但是本着本人比较洁癖的心理习惯，还是想弄个明白。先仔细研究了QComboBox的[文档](http://doc.qt.io/qt-4.8/qcombobox.html)，没有发现什么端倪，也没有在使用QComboBox时有特别的使用习惯。然后我开始调整代码，看是怎么回事：

### 2. 错误可以消失的代码：

```
import sys
from PyQt4.QtGui import *
from PyQt4.QtCore import Qt


if __name__ == "__main__":
    app = QApplication(sys.argv)

    from PyQt4.QtGui import QDialog
    mywin = QDialog()
    mywin.setMinimumSize(400, 400)

    model = QStandardItemModel()

    for i, word in enumerate(['hola', 'adios', 'hello', 'good bye']):
        item = QStandardItem(word)
        model.setItem(i, 0, item)

    combo = QComboBox(mywin)
    combo.setModel(model)

    mywin.show()

    sys.exit(app.exec_())
```

如果仔细的比较两段代码，可以发现实际上是只把`combo.setEditable(True)`删除了。PyQt4种QComboBox的默认值是`setEditable(False)`，那这里的`setEditable(True)`就是问题的`root cause`了。

### 3. 再看另外一段代码：

我们如果想使用和保留`setEditable(True)`，那么我们可以尝试不用PyQt的MVC模式，而直接在QComboBox里插入items，如下代码：

```
import sys
from PyQt4.QtGui import *
from PyQt4.QtCore import Qt


if __name__ == "__main__":
    app = QApplication(sys.argv)

    from PyQt4.QtGui import QDialog
    mywin = QDialog()
    mywin.setMinimumSize(400, 400)

    # model = QStandardItemModel()
    #
    # for i, word in enumerate(['hola', 'adios', 'hello', 'good bye']):
    #     item = QStandardItem(word)
    #     model.setItem(i, 0, item)

    combo = QComboBox(mywin)
    combo.addItems(['hola', 'adios', 'hello', 'good bye'])
    # combo.setModel(model)

    mywin.show()

    sys.exit(app.exec_())
```

可以发现，没有什么问题，如果我们不使用model。

### 4. 我们想使用MVC，而且也要使用`QComboBox.setEditable(True)`

我们使用前面三段的对比代码，可以知道，问题的根源在于PyQt的垃圾回收机制：
- PyQt是对Qt的python封装，PyQt的底层AWT（Abstract Widget Toolkit）实现是由Qt实现的
- Qt是由`C++`实现的
- PyQt的垃圾回收涉及两部分内容：
    - Python部分的垃圾回收
    - `C++`部分的垃圾回收

因此我们在外部使用Python创建的Model，需要显式(explicitly)地指定parent，以便底层的Qt可以正确取得它
的控制权。

因此，要修复我们的示例就很简单了：

```
import sys
from PyQt4.QtGui import *
from PyQt4.QtCore import Qt


if __name__ == "__main__":
    app = QApplication(sys.argv)

    from PyQt4.QtGui import QDialog
    mywin = QDialog()
    mywin.setMinimumSize(400, 400)
    combo = QComboBox(mywin)

    model = QStandardItemModel(combo)

    for i, word in enumerate(['hola', 'adios', 'hello', 'good bye']):
        item = QStandardItem(word)
        model.setItem(i, 0, item)

    combo.setModel(model)

    mywin.show()

    sys.exit(app.exec_())
```

仔细比较前面的代码，我们可以看到改动点是`model = QStandardItemModel(combo)`，
这里显式的指定了combo控件对象作为model的父对象，然后Qt在资源回收时，就能确定
正确的资源回收顺序，而不会导致开头的错误出现。

### 5. 一个更大型的示例：

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from PyQt4.QtCore import Qt
from PyQt4.QtGui import QCompleter, QComboBox, QSortFilterProxyModel, QDialog


class ExtendedComboBox(QComboBox):
    def __init__(self, parent=None):
        super(ExtendedComboBox, self).__init__(parent)

        self.setFocusPolicy(Qt.StrongFocus)
        self.setEditable(True)

        self.filter_model = QSortFilterProxyModel(self)
        self.filter_model.setFilterCaseSensitivity(Qt.CaseInsensitive)
        self.filter_model.setSourceModel(self.model())

        self.completer = QCompleter(self.filter_model, self)
        self.completer.setCompletionMode(QCompleter.UnfilteredPopupCompletion)
        self.setCompleter(self.completer)

        self.lineEdit().textEdited[unicode].connect(
            self.filter_model.setFilterFixedString)
        self.completer.activated.connect(self.on_completer_activated)

    def on_completer_activated(self, text):
        if text:
            index = self.findText(text)
            self.setCurrentIndex(index)

    def setModel(self, model):
        super(ExtendedComboBox, self).setModel(model)
        self.filter_model.setSourceModel(model)
        self.completer.setModel(self.filter_model)

    def setModelColumn(self, column):
        self.completer.setCompletionColumn(column)
        self.filter_model.setFilterKeyColumn(column)
        super(ExtendedComboBox, self).setModelColumn(column)


if __name__ == "__main__":
    import sys
    from PyQt4.QtGui import QApplication, QStandardItemModel
    from PyQt4.QtGui import QStandardItem, QVBoxLayout

    app = QApplication(sys.argv)
    win = QDialog()
    win.setMinimumSize(400, 400)
    combo = ExtendedComboBox()
    layout = QVBoxLayout()
    win.setLayout(layout)
    model = QStandardItemModel(combo)
    for i, word in enumerate(['hola', 'adios', 'hello', 'good bye']):
        item = QStandardItem(word)
        model.setItem(i, 0, item)

    combo.setModel(model)
    layout.addWidget(combo)
    win.show()

    sys.exit(app.exec_())
```
