title: async SSH
date: 2016-01-25 10:37:16
tags:
- SSH
categories:
- 方案
---

项目地址：<https://github.com/ronf/asyncssh>

> AsyncSSH is a Python package which provides an asynchronous client and server implementation of the SSHv2 protocol on top of the Python asyncio framework. It requires Python 3.4 or later and the Python cryptography library for some cryptographic functions.

- 基于Python3.4+
- 实现了SSHv2协议
- 基于asyncio框架

```
import asyncio, asyncssh, sys

@asyncio.coroutine
def run_client():
    with (yield from asyncssh.connect('localhost')) as conn:
        stdin, stdout, stderr = yield from conn.open_session('echo "Hello!"')
        output = yield from stdout.read()
        print(output, end='')

        status = stdout.channel.get_exit_status()
        if status:
            print('Program exited with status %d' % status, file=sys.stderr)
        else:
            print('Program exited successfully')

asyncio.get_event_loop().run_until_complete(run_client())
```
