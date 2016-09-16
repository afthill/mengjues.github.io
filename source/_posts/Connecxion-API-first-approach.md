title: 'Connecxion: API first approach'
date: 2016-09-17 00:49:57
tags:
- Python
- API
- Swagger
categories:
- 方案
---

微服务（microservice）现在蛮流行的，Connecxion是Python平台为微服务架构
提供的一个方案。

### API First Approach

API First Approach的开发逻辑第一次把API specification作为一类对象和其他
的设计说明书一起放在开发的流程中，所以在设计API时需要更多的考虑：包括API的
消费使用情况、模拟、文档、性能等。

Google、IBM、Microsoft等其他公司一起成立里开放API联盟（Open API Initiative），
用来规范和统一restful API的定义、格式等，这个specification被叫做Open API
Specification，之前被叫做Swagger2.0。这些open api定义的格式可以很容易的用
一些代码生成器（code generator）来生成。

Connecxion的特点是：
- 不依赖于code generator也不依赖code boilerplate。
- 基于Flask。

### 示例

第一步是：找出当前要提供的资源如何使用openapi的格式来展示，比如：

```
swagger: '2.0'
info:
  title: Hello API
  version: "0.1"
paths:
  /greeting:
    get:
      operationId: api.say_hello
      summary: Returns a greeting.
      responses:
        200:
          description: Successful response.
          schema:
            type: object
            properties:
              message:
                type: string
                description: Message greeting
```

然后python处理api的代码：

```
# `api.py` file contents
def say_hello():
    return {"message": "Hello API!"}
```

再运行server：

```
# run this command in the same directory where you saved the previous files.
$ pip install connexion # installs connexion, run only once.
$ connexion run my_api.yaml -v
```

打开浏览器：`http://localhost:5000/greeting`，将会收到：

```
{"message": "Hello API!"}
```





原文：http://caricio.com/2016/09/16/crafting-effective-microservices-in-python/
