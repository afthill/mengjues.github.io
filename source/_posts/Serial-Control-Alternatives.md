layout: post
title: "Serial port control solution alternatives"
date: 2016-05-25 12:15
tags:
- Serial Port
categories:
- 方案
---

### 1) pexpect

官网：           
https://github.com/pexpect

示例：

```
fd = os.open("/dev/ttyS0", os.O_NONBLOCK|os.O_RDWR|os.O_NOCTTY)
th = pexpect.fdpexpect.fdspawn(fd)
th.expect(["stuff", pexpect.TIMEOUT], 5)
```

### 2) python streamexpect

官网：         
https://github.com/digidotcom/python-streamexpect

示例：        
```
import serial
import streamexpect

# timeout=0 is essential, as streams are required to be non-blocking
ser = serial.Serial('COM1', baudrate=115200, timeout=0)

with streamexpect.wrap(ser) as stream:
  stream.write('\r\nuname -a\r\n')
  match = stream.expect_bytes('Linux', timeout=1.0)
  print(u'Found Linux at index {}'.format(match.start))
```
