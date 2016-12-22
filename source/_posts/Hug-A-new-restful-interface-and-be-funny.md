title: 'Hug: A new restful interface and be funny'
date: 2016-12-23 01:11:14
tags:
- Hug
categories:
- 方案
---

http://www.hug.rest/

很好玩的一个应用， Python3 Only，但是性能也是不错。

```
"""An example of writing an API to scrape hacker news once, and then enabling usage everywhere"""
import hug
import requests


@hug.local()
@hug.cli()
@hug.get()
def top_post(section: hug.types.one_of(('news', 'newest', 'show'))='news'):
    """Returns the top post from the provided section"""
    content = requests.get('https://news.ycombinator.com/{0}'.format(section)).content
    text = content.decode('utf-8')
    return text.split('<tr class=\'athing\'>')[1].split("<a href")[1].split(">")[1].split("<")[0]
```    