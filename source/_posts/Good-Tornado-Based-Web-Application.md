layout: post
title: "Tornado Basic and Demo"
date: 2015-04-10 16:31:37
tags:
- Python
- Tornado
categories:
- 技术
---

### Long Polling with tornado
Tornado的异步架构可以对HTTP的long polling得心应手，所谓的long polling是一种技术用来
处理实时提醒或者构建高性能的多人聊天室。早期实现这种技术是使用在客户端定时更新的方法，
但是带来的挑战是“实时更新”的“频率”必须足够的快，但是太快的客户端请求又会使服务器不堪
重负。

所以现在使用一种叫做“Server Push”的技术，要求在“网络应用”端在有状态（state）更新（
update）时，实时推送给客户端。在现在的常见的server端push技术中，需要跟客户端的浏览器
有很好的兼容，所以最流行的技术使用的是，通过客户端发起的连接去模拟“Server Push”。这类
有客户端发起的用来server push的连接，叫做“Long Polling”或者“Comet Request”。

Long Polling技术是客户端发起一个连接，并保持在open状态，然后客户端等待服务器端“推送”
状态更新。当服务器端推送更新完成后，将会关闭该连接。然后，客户端打开另外一个新的连接，
等待服务器端的另外一次推送。

### Benefits of Long Polling
- 减少服务器负载
- 服务端只在客户端发起连接后，推送状态更新
- 浏览器只需要支持AJAX
- 相比其他的推送技术，“Long Polling”得到更广泛的应用
- 参考实现：
    - Google Doc使用Long Polling实现实时文档编辑与协作
    - Twitter使用long polling去更新用户的提醒
    - Facebook使用long polling实现聊天

### Tornado最佳代码结构：
- <https://github.com/bueda/tornado-boilerplate>
- <https://github.com/hkage/tornado-project-skeleton>
- <https://github.com/iambibhas/tornado-boilterplate>
- <https://github.com/decultured/Tornado-Base-Project>

### Tornado示例站点
1. <https://github.com/ajdavis/motor-blog>
2. <https://github.com/viewfinderco/viewfinder/>
3. <https://github.com/ipython/ipython/tree/master/IPython/html>
4. <https://github.com/cdkr/tornado-cantas>
5. <https://github.com/nellessen/tornado-shortener>
6. <https://github.com/livid/v2ex>
7. <https://github.com/rande/python-element>
8. <http://thomas.rabaix.net/under-the-hood>
9. <https://github.com/abdelouahabb/essog>
10. <https://github.com/mitotic/graphterm>
11. <https://github.com/peterbe/worklog>
12. <https://github.com/peterbe/toocool>
13. <https://github.com/martinrusev/amon>
14. <https://github.com/tornadoweb/tornado>
15. <https://github.com/peterbe/tornado_gists>
16. <https://github.com/bgolub/tornado-blog>
17. <https://github.com/peterbe/tiler>
18. <https://bitbucket.org/black_rez/lola21/>
19. <https://github.com/finiteloop/socialcookbook>
20. <https://github.com/familonet/>
21. <https://github.com/GovLab/OpenData500>

### 参考：
- <http://stackoverflow.com/questions/15415634/whats-the-best-structure-for-a-large-scale-python-tornado-project>
