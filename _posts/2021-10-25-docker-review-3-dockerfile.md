---
title: Docker Review 3 (Dockerfile 镜像定制)
tags: [Docker]
---

## EXPOSE 暴露端口
格式为 `EXPOSE <端口1> [<端口2>...]`。`EXPOSE` 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。

## WORKDIR 指定工作目录
格式为 `WORKDIR <工作目录路径>`。使用 `WORKDIR` 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录，如该目录不存在，`WORKDIR` 会帮你建立目录。

在 Shell 中，连续两行是同一个进程执行环境，因此前一个命令修改的内存状态，会直接影响后一个命令；而在 `Dockerfile` 中，这两行 `RUN` 命令的执行环境根本不同，是两个完全不同的容器。这就是对 `Dockerfile` 构建分层存储的概念不了解所导致的错误：
```
RUN cd /app
RUN echo "hello" > world.txt
```

因此如果需要改变以后各层的工作目录的位置，那么应该使用 `WORKDIR` 指令：
```
WORKDIR /app

RUN echo "hello" > world.txt
```

如果你的 `WORKDIR` 指令使用的相对路径，那么所切换的路径与之前的 `WORKDIR` 有关：
```
WORKDIR /a
WORKDIR b
WORKDIR c

RUN pwd # '/a/b/c'
```

## USER 指定当前用户
格式：`USER <用户名>[:<用户组>]`。`USER` 指令和 `WORKDIR` 相似，都是改变环境状态并影响以后的层。`WORKDIR` 是改变工作目录，`USER` 则是改变之后层的 `RUN`、`CMD` 以及 `ENTRYPOINT` 这类命令的身份。

## 多阶段构建镜像：多个 FROM 指令的意义
最后生成的镜像，仍以最后一条 `FROM` 为准，之前的 `FROM` 会被抛弃，那么之前的 `FROM` 又有什么意义呢？

每一条 `FROM` 指令都是一个构建阶段，多条 `FROM` 就是多阶段构建，虽然最后生成的镜像只能是最后一个阶段的结果，但是，能够将前置阶段中的文件拷贝到后边的阶段中，这就是多阶段构建的最大意义。

使用场景之一是将编译环境和运行环境分离：
```
# 编译阶段
FROM golang:1.10.3

COPY server.go /build/

WORKDIR /build

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 GOARM=6 go build -ldflags '-w -s' -o server

# 运行阶段
FROM scratch

# 从编译阶段的中拷贝编译结果到当前镜像中
COPY --from=0 /build/server /

ENTRYPOINT ["/server"]
```

这个 `Dockerfile` 的玄妙之处就在于 `COPY` 指令的 `--from=0` 参数，从前边的阶段中拷贝文件到当前阶段中，多个 `FROM` 语句时，`0` 代表第一个阶段。除了使用数字，我们还可以给阶段命名，比如：
```
# 编译阶段 命名为 builder
FROM golang:1.10.3 as builder

# ... 省略

# 运行阶段
FROM scratch

# 从编译阶段的中拷贝编译结果到当前镜像中
COPY --from=builder /build/server /
```

`COPY --from` 不但可以从前置阶段中拷贝，还可以直接从一个已经存在的镜像中拷贝。比如：
```
FROM ubuntu:16.04
    
COPY --from=quay.io/coreos/etcd:v3.3.9 /usr/local/bin/etcd /usr/local/bin/
#直接将官方编译好的程序文件拿过来
```

## References
- [Dockerfile多阶段构建原理和使用场景](https://segmentfault.com/a/1190000016137548)
- [Docker —— 从入门到实践](https://yeasy.gitbook.io/docker_practice/image/multistage-builds/laravel)
