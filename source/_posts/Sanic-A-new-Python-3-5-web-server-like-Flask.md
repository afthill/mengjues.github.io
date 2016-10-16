title: 'Sanic: A new Python 3.5 web server like Flask'
date: 2016-10-16 19:10:09
tags:
- Web Server
categories:
- 方案
---

首页：https://github.com/channelcat/sanic

### Benchmarks

| Server  | Implementation      | Requests/sec | Avg Latency |
| ------- | ------------------- | ------------:| -----------:|
| Sanic   | Python 3.5 + uvloop |       30,601 |      3.23ms |
| Wheezy  | gunicorn + meinheld |       20,244 |      4.94ms |
| Falcon  | gunicorn + meinheld |       18,972 |      5.27ms |
| Bottle  | gunicorn + meinheld |       13,596 |      7.36ms |
| Flask   | gunicorn + meinheld |        4,988 |     20.08ms |
| Kyoukai | Python 3.5 + uvloop |        3,889 |     27.44ms |
| Aiohttp | Python 3.5 + uvloop |        2,979 |     33.42ms |
