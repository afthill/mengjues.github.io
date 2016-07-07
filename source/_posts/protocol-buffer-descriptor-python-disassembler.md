title: Protocol Buffer Descriptor Python Disassembler
date: 2016-01-26 10:54:03
tags:
- Protocol Buffer Descriptor
categories:
- Persistence
---

项目地址：<https://github.com/rsc-dev/pbd>

**首先什么是Protocol Buffer Descriptor？**

<https://developers.google.com/protocol-buffers/>

The example we're going to use is a very simple "address book" application that can read and write people's contact details to and from a file. Each person in the address book has a name, an ID, an email address, and a contact phone number.

How do you serialize and retrieve structured data like this? There are a few ways to solve this problem:

- Use Python pickling. This is the default approach since it's built into the language, but it doesn't deal well with schema evolution, and also doesn't work very well if you need to share data with applications written in C++ or Java.
- You can invent an ad-hoc way to encode the data items into a single string – such as encoding 4 ints as "12:3:-23:67". This is a simple and flexible approach, although it does require writing one-off encoding and parsing code, and the parsing imposes a small run-time cost. This works best for encoding very simple data.
- Serialize the data to XML. This approach can be very attractive since XML is (sort of) human readable and there are binding libraries for lots of languages. This can be a good choice if you want to share data with other applications/projects. However, XML is notoriously space intensive, and encoding/decoding it can impose a huge performance penalty on applications. Also, navigating an XML DOM tree is considerably more complicated than navigating simple fields in a class normally would be.

Protocol buffers are the flexible, efficient, automated solution to solve exactly this problem. With protocol buffers, you write a .proto description of the data structure you wish to store. From that, the protocol buffer compiler creates a class that implements automatic encoding and parsing of the protocol buffer data with an efficient binary format. The generated class provides getters and setters for the fields that make up a protocol buffer and takes care of the details of reading and writing the protocol buffer as a unit. Importantly, the protocol buffer format supports the idea of extending the format over time in such a way that the code can still read data encoded with the old format.

**使用示例**

```
>python -m pbd -f examples\test.ser

 _|_|_|    _|              _|
 _|    _|  _|_|_|      _|_|_|
 _|_|_|    _|    _|  _|    _|
 _|        _|    _|  _|    _|
 _|        _|_|_|      _|_|_|

                      ver 0.9

[+] Paring file test.ser
[+] Proto file saved as .\test.proto
>type test.proto
// Reversed by pbd (https://github.com/rsc-dev/pbd)
// Package not defined

message Person {
        required string name = 1 ;
        required int32 id = 2 ;
        optional string email = 3 ;
}
```
