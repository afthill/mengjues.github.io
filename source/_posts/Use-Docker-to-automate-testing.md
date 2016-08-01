title: Use Docker to automate testing
date: 2016-07-12 09:42:29
tags:
- Docker
categories:
- 自动化
---

缘由：在测试中需要启动一些后台进程，但是后台进程启动的非常慢，严重影响了自动化测试。

问题：需要快速的启动或者关闭一些后台进程，必须redis、memcache等。

方案：使用docker这种轻量级的虚拟机（container）。

-------------------------

### 安装docker-py

```
pip install docker-py
```

### 定义docker

```
postgres = dict(image="postgres", 
                prots=["5432:5432",],
                environment={
                    'POSTGRES_USER': 'test',
                    'POSTGRES_PASS': 'test'
                }
           )

rabbit = dict(image="rabbitmq:3-management",
              ports=["15672:15672", "5762:5762"]
         )

container = [postgres, rabbit]
```

### 定义class level的setUp和tearDown

```
import docker
class DockerUnitTestExample(unittest.TestCase):
    @classmethod
    def initContainer(cls):
        postgres = dict(image="postgres",
            ports=["5432:5432",],
            envrionment={
                "POSTGRES_USER": "test",
                "POSTGRES_PASSWORD": "test"
            }
        )

        container = [postgres,]

        for container in containers:
            cls.dockClient.pull(container["image"])
            cls.containers.append(cls.dockerClient.create_container(**container)
    @classmethod
    def runContainers(cls):
        for container in cls.containers:
            cls.dockerClient.start(container["Id"])

    @classmethod
    def stopContainers(cls):
        for container in cls.containers:
            cls.dockerClient.stop(container["Id"])

    @classmethod
    def setUpClass(cls):
        cls.containers = []
        cls.dockerClient = docker.Client()
        cls.dockerClient.login('dockerhubuser', 'dockerhubuser')
        cls.initContainers()
        cls.runContainers()

    @classmethod
    def tearDownClass(cls):
        cls.stopContainers()
```

参考： <http://www.talperry.com/2015/10/03/python-unittests-with-docker/>
