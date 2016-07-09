layout: post
title: "Windows CTRL C Event and the workaround"
date: 2015-04-21 14:24
tags:
- Windows_CTRL_C
- PyQt
- Qt
categories:
- Python
---


#### 参考：

##### 模拟方案
1. <https://groups.google.com/forum/#!topic/qbzr/IIHVFZ7nJ2s>
2. <https://03255835288185997573.googlegroups.com/attach/d18031297c011b70/stop-subprocess-win32-573.patch?part=0.1&view=1&vt=ANaJVrEjLEPxrT41e9rqB8xbnMLFagAr7UR43c1-EHL-_22nmm8WoWynjacx2d3fFdjnRp-MmrrLeYWScsRSKhymFoR-BdQ8_xiuLSck2h1MeiQIIuOZz9M>
3. <https://github.com/jcanalesc/Everton-Pulseras/blob/master/winctrlc.py>
4. <http://stackoverflow.com/questions/7085604/sending-c-to-python-subprocess-objects-on-windows>
5. <https://bugs.launchpad.net/qbzr/+bug/277082>
6. <http://svn.smedbergs.us/python-processes/trunk/>
7. <https://groups.google.com/forum/#!topic/qbzr/kVi-CgvUzUM>





##### WIN原生方案
1. <https://bugreports.qt.io/browse/QTBUG-24619>
2. <http://stanislavs.org/stopping-command-line-applications-programatically-with-ctrl-c-events-from-net/>
3. <http://www.qtcentre.org/threads/36911-Can-t-send-a-Ctrl-C-to-a-QProcess-%28Win32%29>
4. <https://hg.python.org/cpython/file/default/Lib/test/test_os.py>
5. <https://hg.python.org/cpython/file/44253ce374fc/Lib/test/win_console_handler.py>
6. <https://hg.python.org/cpython/rev/384d2189a96a/>
7. <https://bugs.python.org/issue9524>
8. <https://docs.python.org/2/library/signal.html>
9. <https://bitbucket.org/weyou/winpexpect/src/74fa12e233f43136248292a578a107f2e0e56775/lib/winpexpect.py?at=default>
10. <http://stackoverflow.com/questions/13024532/simulate-ctrl-c-keyboard-interrupt-in-python-while-working-in-linux>
11. <http://stackoverflow.com/questions/4669204/send-ctrl-c-to-remote-processes-started-via-subprocess-popen-and-ssh>
12. <http://pastebin.com/tNCLbevw>


##### 其他
1. <http://doc.qt.io/qt-5/qprocess-members.html>
2. <https://docs.python.org/2/library/thread.html#thread.interrupt_main>
3. <https://launchpad.net/qbzr>
