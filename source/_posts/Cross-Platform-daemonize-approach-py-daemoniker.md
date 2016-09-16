title: 'Cross Platform daemonize approach: py_daemoniker'
date: 2016-09-17 01:18:57
tags:
- daemonize
categories:
- 方案
---

> ### Why daemonization?

> Python on Windows ships with pythonw.exe, which provides a "GUI app with no GUI" to run Python in the background. But using it precludes ever using the terminal to exchange information with the process, even to start it. Developers wanting to create Windows background processes are forced to choose between writing their own system-tray-minimizable GUI application using pythonw.exe (or a freezing packager like pyinstaller), or to define and install their code as a service in the Win32 API. There is a pretty clear gap for a dead-simple way to "daemonize" running code on Windows, exactly as you would on a Unix system.

more: https://github.com/Muterra/py_daemoniker

