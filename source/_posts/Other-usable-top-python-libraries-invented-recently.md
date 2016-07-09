title: 几个有用的python库介绍
date: 2015-12-29 16:42:49
tags:
- Python Libs
categories:
- 推荐

---


### Folium

项目地址：<https://github.com/python-visualization/folium>

<img src="https://camo.githubusercontent.com/7294b89c91bd081c7224a0f6903b2e423418f432/687474703a2f2f6661726d332e737461746963666c69636b722e636f6d2f323838332f383735353933373931325f316439656637383131385f632e6a7067">

基于Leaflet.js地图库的python实现，主要是python在前端生成、控制和操作数据，而后端地图
的实现则给予Leaflet.js。


### tinydb

项目地址：<http://tinydb.readthedocs.org/en/latest/>

NoSQL数据库的的纯python实现，可以用于快速的原型开发。

```
>>> from tinydb import TinyDB, Query
>>> db = TinyDB('path/to/db.json')
>>> User = Query()
>>> db.insert({'name': 'John', 'age': 22})
>>> db.search(User.name == 'John')
[{'name': 'John', 'age': 22}]
```

### dill

项目地址：<http://trac.mystic.cacr.caltech.edu/project/pathos/wiki/dill>

pickle的更优替代，由于pickle不能处理嵌套的function，lambda，slices，接口与
pickle是一模一样的。
