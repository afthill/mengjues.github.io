title: How to build a websocketd based program
date: 2016-10-31 16:12:26
tags:
- Websocketd
categories:
- 方案
---

做云测平台时，需要的一个问题，就是需要测试机与平台之间的双向交互。开始的时候大概选定的方案是restful interface+message queue，后来发现这个方案的问题在于测试机要大量的配置文件，而且使用message queue的问题在于，message queue在浏览器要实时处理传输过来的log和文件的难度很大，经过仔细斟酌，初步选定[websocketd](http://websocketd.com/)，但是这个websocket的问题在于：
- 他只是简单接收stdin和发射stdout，并且每一个新的连接都fork一个新的当前运行的进程。
- 他不支持middleware，导致这个曝露出去的端口，没有任何防护和认证。

所以要处理这些问题，初步想到的解决方案：
- 在浏览器和websocketd之间，增加一层代理，然后通过代理处理往返信息和与websocketd之间的交互。
- 在websocket的前面增加nginx，使用nginx做代理，然后反向转发到websocket。

还在思考中。。。也许以后会有更多想法和具体的实现有出入。
