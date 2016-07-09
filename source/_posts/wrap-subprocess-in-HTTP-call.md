title: 把shell调用曝露给HTTP API？
date: 2016-01-08 09:48:37
tags:
- Shell
- HTTP API
categories:
- 推荐

---

项目地址：<https://github.com/lukasmartinelli/nigit>

该项目的主要目的是把OS上的subprocess转化为直接的HTTP接口，示例如下：

1. 比如我们想使用一个html转pdf的服务，我们之前的shell脚本如下：
    ```
    #!/bin/bash
    wkhtmltopdf "$URL" page.pdf > /dev/null 2&>1
    cat page.pdf
    ```

2. 我们可以通过nigit启动服务器：`nigit html2pdf.sh `

3. 然后我们可以用curl调用HTTP接口：
    ```
    curl -o google.pdf http://localhost:8000/html2pdf?url=http://google.com
    ```

-END-
