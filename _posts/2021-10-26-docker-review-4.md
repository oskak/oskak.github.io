---
title: Docker Review 4
tags: [Docker]
---

## 容器概念
简单的说，容器是独立运行的一个或一组应用，以及它们的运行态环境。对应的，虚拟机可以理解为模拟运行的一整套操作系统（提供了运行态环境和其他系统环境）和跑在上面的应用。

## 启动容器
启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（exited）的容器重新启动。

例如，下面的命令输出一个“Hello World”后终止容器：
```
$ docker run ubuntu:18.04 /bin/echo 'Hello world'
Hello world
```

启动一个 bash 终端，允许用户进行交互：
```
$ docker run -t -i ubuntu:18.04 /bin/bash
root@af8bae53bdd3:/#
```

其中，`-t` 选项让 Docker 分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上，`-i` 则让容器的标准输入保持打开。

当用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：
- 检查本地是否存在指定的镜像，不存在就从 registry 下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

可以用 `docker container start` 命令，将一个已经终止（exited）的容器启动运行。

容器的核心为所执行的应用程序，所需要的资源都是应用程序运行所必需的。除此之外，并没有其它的资源。可以在伪终端中用 `ps` 或 `top` 来查看进程信息：
```
root@ba267838cc1b:/# ps
  PID TTY          TIME CMD
    1 ?        00:00:00 bash
   11 ?        00:00:00 ps
```

可见，容器中仅运行了指定的 bash 应用。这种特点使得 Docker 对资源的利用率极高，是货真价实的轻量级虚拟化。

## 守护态运行
使用 `-d` 参数运行容器：
```
$ docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
```

使用 `-d` 参数启动后会返回一个唯一的 id，也可以通过 `docker container ls` 命令来查看容器信息。

要获取容器的输出信息，可以通过 `docker container logs` 命令：
```
$ docker container logs [container ID or NAMES]
hello world
hello world
hello world
. . .
```

## 终止容器
当 Docker 容器中指定的应用终结时，容器也自动终止。终止状态的容器可以用 `docker container ls -a` 命令看到。可以使用 `docker container stop` 来终止一个运行中的容器。处于终止状态的容器，可以通过 `docker container start` 命令来重新启动。此外，`docker container restart` 命令会将一个运行态的容器终止，然后再重新启动它。

## 进入容器
`docker exec` 后边可以跟多个参数：
```
$ docker run -dit ubuntu
69d137adef7a8a689cbcb059e94da5489d3cddd240ff675c640c8d96e84fe1f6

$ docker container ls

$ docker exec -it 69d1 bash
root@69d137adef7a:/#
```

如果从这个 `stdin` 中 exit，不会导致容器的停止。这就是为什么推荐大家使用 `docker exec` 的原因。

## 删除容器
可以使用 `docker container rm` 来删除一个处于终止状态的容器。用 `docker container ls -a` 命令可以查看所有已经创建的包括终止状态的容器，如果数量太多要一个个删除可能会很麻烦，用下面的命令可以清理掉所有处于终止状态的容器：
```
$ docker container prune
```

## 数据卷
数据卷是一个可供一个或多个容器使用的特殊目录，提供很多有用的特性：
- 数据卷可以在容器之间共享和重用
- 对数据卷的修改会立马生效
- 对数据卷的更新，不会影响镜像
- 数据卷默认会一直存在，即使容器被删除

创建一个数据卷：
```
$ docker volume create my-vol
```

查看所有的数据卷：
```
$ docker volume ls

DRIVER              VOLUME NAME
local               my-vol
```

在主机里使用以下命令可以查看指定数据卷的信息：
```
$ docker volume inspect my-vol
[
    {
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]
```

启动一个挂载数据卷的容器（此处挂载一个本地主机目录作为数据卷）：
```
$ docker run -d -P \
    --name web \
    --mount source=my-vol,target=/usr/share/nginx/html \
    nginx:alpine
```

在主机里使用以下命令可以查看 `web` 容器的信息：
```
$ docker inspect web

"Mounts": [
    {
        "Type": "volume",
        "Name": "my-vol",
        "Source": "/var/lib/docker/volumes/my-vol/_data",
        "Destination": "/usr/share/nginx/html",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],
```

删除数据卷：
```
$ docker volume rm my-vol
```

无主的数据卷可能会占据很多空间：
```
$ docker volume prune
```

Docker 挂载主机目录的默认权限是**读写**，用户也可以通过增加 `readonly` 指定为**只读**：
```
$ docker run -d -P \
    --name web \
    --mount type=bind,source=/src/webapp,target=/usr/share/nginx/html,readonly \
    nginx:alpine
```

挂载一个本地主机文件作为数据卷：
```
$ docker run --rm -it \
   --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history \
   ubuntu:18.04 \
   bash

root@2affd44b4667:/# history
1  ls
2  diskutil list
```

这样就可以记录在容器输入过的命令了。

## References
- [Docker —— 从入门到实践](https://yeasy.gitbook.io/docker_practice/network/port_mapping)
