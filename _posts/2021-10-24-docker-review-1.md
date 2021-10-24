---
title: Docker Review 1
tags: [Docker]
---

## What is Hypervisor?
Hypervisor，又称虚拟机监控器（Virtual Machine Monitor，缩写为 VMM），是用来创建与运行虚拟机的软件、固件或硬件。被 hypervisor 用来运行一个或多个虚拟机的电脑称为宿主机（host machine），这些虚拟机则称为客户机（guest machine）。Hypervisor 提供虚拟的作业平台来运行客户操作系统（guest operating systems）；这些客操作系统，共同分享虚拟化后的硬件资源。

## What is Docker?
Linux 发展出了另一种虚拟化技术：Linux 容器（Linux Containers，缩写为 LXC）。Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。或者说，在正常进程的外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。

Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。它是目前最流行的 Linux 容器解决方案。Docker 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 Docker，就不用担心环境问题。

总体来说，Docker 的接口相当简单，用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。

Docker 容器与虚拟机类似，但二者在原理上不同。容器是将操作系统层虚拟化，虚拟机则是虚拟化硬件，因此容器更具有便携性。容器更多的用于表示软件的一个标准化单元。由于容器的标准化，因此它可以无视基础设施（Infrastructure）的差异，部署到任何一个地方。另外，Docker 也为容器提供更强的业界的隔离兼容。

为什么要用 Docker？
- 更高效地利用系统资源
- 更快速的启动时间
- 一致的运行环境
- 持续交付和部署
- 更轻松的迁移
- 更轻松的维护和扩展

## Docker 架构简介
Docker 使用 `客户端-服务器` 架构。Docker 客户端（client）与守护进程（Docker daemon/dockerd）进行对话，该守护进程完成了构建、运行和分发 Docker 容器的繁重工作。Docker 客户端和守护进程可以在同一系统上运行，或者您可以将 Docker 客户端连接到远程 Docker 守护进程。

Docker Engine 包括这几个部分：
- Docker Daemon：docker 的守护进程，属于 C/S 中的 server
- Docker REST API：docker daemon 向外暴露的 REST 接口
- Docker CLI：docker 向外暴露的命令行接口（Command Line API）

## Docker 主要用途
目前有三大类：
1. 提供一次性的环境；
2. 提供弹性的云服务；
3. 组建微服务架构。

## Docker 基本概念
Docker 包括三个基本概念：
- 镜像（Image）
- 容器（Container）
- 仓库（Repository）

理解了这三个概念，就理解了 Docker 的整个生命周期。

### 镜像
操作系统分为**内核**和**用户空间**。对于 Linux 而言，内核启动后，会挂载 `root` 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 `root` 文件系统。比如官方镜像 `ubuntu:18.04` 就包含了完整的一套 Ubuntu 18.04 最小系统的 `root` 文件系统。

Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

### 容器
镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。因此容器可以拥有自己的 `root` 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全。

镜像使用的是分层存储，容器也是如此。每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为**容器存储层**。容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据不会丢失。

### 仓库
一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 `<仓库名>:<标签>` 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 `latest` 作为默认标签。

## 一些命令、知识
```
#获取镜像
$ docker pull ubuntu:18.04

#列出镜像
$ docker image ls
$ docker image ls -a
$ docker image ls ubuntu
$ docker image ls ubuntu:18.04
$ docker image ls -f before=ubuntu:18.04 #过滤器参数
$ docker image ls -f since=ubuntu:18.04

$ docker image ls -q
5f515359c7f8
05a60462f8ba
fe9198c04d62
```

镜像 ID 则是镜像的唯一标识，一个镜像可以对应多个标签。

查看镜像、容器、数据卷所占用的空间：
```
$ docker system df
```

```
#删除镜像
$ docker image rm 9364575
$ docker image rm -f $(docker image ls -q redis)
```

删除行为分为两类，一类是 Untagged，另一类是 Deleted。所以并非所有的 `docker image rm` 都会产生删除镜像的行为，有可能仅仅是取消了某个标签而已。

## docker commit
使用 `docker commit` 意味着所有对镜像的操作都是黑箱操作，生成的镜像也被称为黑箱镜像，换句话说，就是除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知。

## 虚悬镜像
```
$ docker image ls -f dangling=true
$ docker image prune #用命令删除
```

## 中间层镜像
为了加速镜像构建、重复利用资源，Docker 会利用**中间层镜像**。所以在使用一段时间后，可能会看到一些依赖的中间层镜像。默认的 `docker image ls` 列表中只会显示顶层镜像，如果希望显示包括中间层镜像在内的所有镜像的话，需要加 `-a` 参数：
```
$ docker image ls -a
```

### 运行第一个容器
以 `ubuntu:18.04` 镜像为基础启动并运行一个容器。如果打算启动里面的 `bash` 并且进行交互式操作的话，可以执行下面的命令：
```
$ docker run -it --rm ubuntu:18.04 bash
```

`--rm`：这个参数是说容器退出后随之将其删除。

`-it`：这是两个参数，一个是 `-i`：交互式操作，一个是 `-t`：终端。

## References
- [维基 Hypervisor](https://zh.wikipedia.org/zh-cn/Hypervisor)
- [维基 Docker](https://zh.wikipedia.org/zh-cn/Docker)
- [为什么要用 Docker](https://yeasy.gitbook.io/docker_practice/introduction/why)
- [Docker 入门教程](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
