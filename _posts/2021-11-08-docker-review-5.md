---
title: Docker Review 5
tags: [Docker]
---

## 外部访问容器
当使用 `-P` 标记时，Docker 会随机映射一个端口到内部容器开放的网络端口：
```
$ docker run -d -P nginx:alpine

$ docker container ls -l
```

通过 `docker logs` 命令来查看访问日志：
```
$ docker logs <container id>
```

使用 `hostPort:containerPort` 格式，本地的 `80` 端口映射到容器的 `80` 端口（此时默认会绑定本地所有接口上的所有地址）：
```
$ docker run -d -p 80:80 nginx:alpine
```

指定映射使用一个特定地址，比如 `127.0.0.1`：
```
$ docker run -d -p 127.0.0.1:80:80 nginx:alpine
```

绑定 `localhost` 的任意端口到容器的 `80` 端口，本地主机会自动分配一个端口：
```
$ docker run -d -p 127.0.0.1::80 nginx:alpine
```

使用 `docker port` 来查看当前映射的端口配置，也可以查看到绑定的地址：
```
$ docker port <container id> 80
0.0.0.0:32768
```

绑定多个端口：
```
$ docker run -d \
    -p 80:80 \
    -p 443:443 \
    nginx:alpine
```

## 容器互联
新建一个容器网络：
```
$ docker network create -d bridge my-net  # -d 参数指定 Docker 网络类型
```

运行两个容器并连接到新建的 `my-net` 网络：
```
$ docker run -it --rm --name busybox1 --network my-net busybox sh
$ docker run -it --rm --name busybox2 --network my-net busybox sh
```

再打开一个新的终端查看容器信息：
```
$ docker container ls
```

通过 `ping` 来证明 `busybox1` 容器和 `busybox2` 容器建立了互联关系。在 `busybox1` 容器输入以下命令：
```
/ # ping busybox2
PING busybox2 (172.19.0.3): 56 data bytes
64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.072 ms
64 bytes from 172.19.0.3: seq=1 ttl=64 time=0.118 ms
```

如果你有多个容器之间需要互相连接，推荐使用 Docker Compose。

## Docker Compose
Docker Compose 是 Docker 官方编排（Orchestration）项目之一，负责快速地部署分布式应用。Compose 定位是「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」

我们知道使用一个 `Dockerfile` 模板文件，可以让用户很方便地定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，负载均衡容器等。Compose 恰好满足了这样的需求。它允许用户通过一个单独的 `docker-compose.yml` 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。Compose 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷的生命周期管理。

场景示例：最常见的项目是 web 网站，该项目应该包含 web 应用和缓存。

`flask_app/app.py`:
```
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! 该页面已被访问 {} 次。\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

`flask_app/Dockerfile`:
```
FROM python:3.6-alpine
ADD . /code
WORKDIR /code
RUN pip install redis flask
CMD ["python", "app.py"]
```

`flask_app/docker-compose.yml`:
```
version: '3'
services:

  web:
    build: .
    ports:
     - "5000:5000"

  redis:
    image: "redis:alpine"
```

运行 compose 项目，访问本地 `5000` 端口：
```
$ docker compose up
```

## References
- [Docker —— 从入门到实践](https://yeasy.gitbook.io/docker_practice/compose/commands)
