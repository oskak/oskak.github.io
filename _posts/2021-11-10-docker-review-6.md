---
title: Docker Review 6
tags: [Docker]
---

## Compose 模板文件的一些指令
### build
指定 `Dockerfile` 所在文件夹的路径（可以是绝对路径，或者相对 `docker-compose.yml` 文件的路径）。Compose 将会利用它自动构建这个镜像，然后使用这个镜像。

```
version: '3'
services:

  webapp:
    build: ./dir
```

### command
覆盖容器启动后默认执行的命令。

```
command: echo "hello world"
```

### container_name
指定容器名称。默认将会使用 `项目名称-服务名称-序号` 格式。指定容器名称后，该服务将无法进行扩展（scale），因为 Docker 不允许多个容器具有相同的名称。

```
container_name: docker-web-container
```

### depends_on
先启动 `redis`、`db` 再启动 `web`：
```
# web 服务不会等待 redis、db「完全启动」之后才启动
version: '3'

services:
  web:
    build: .
    depends_on:
      - db
      - redis

  redis:
    image: redis

  db:
    image: postgres
```

### 其它指令
```
# entrypoint：指定服务容器启动后执行的入口文件。

entrypoint: /code/entrypoint.sh

# restart：指定容器退出后的重启策略。
# 该命令对保持服务始终运行十分有效，在生产环境中推荐配置为 always 或者 unless-stopped。

restart: always 
```

## 场景示例
使用 Docker Compose 配置并运行一个 Django/PostgreSQL 应用。

`django_app/Dockerfile`:
```
FROM python:3
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
COPY requirements.txt /code/
RUN pip install -r requirements.txt
COPY . /code/
```

`django_app/requirements.txt`:
```
Django>=2.0,<3.0
psycopg2>=2.7,<3.0
```

`django_app/docker-compose.yml`:
```
version: "3"
services:

  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: 'postgres'

  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
```

使用 `docker-compose run` 命令启动一个 Django 应用（将在当前目录生成一个 Django 应用）：
```
$ docker-compose run web django-admin startproject django_example .
```

`django_app/django_example/settings.py`:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'postgres',
        'USER': 'postgres',
        'HOST': 'db',
        'PORT': 5432,
        'PASSWORD': 'postgres',
    }
}
```

运行 `docker compose up`：
```
$ docker compose up # 127.0.0.1:8000
```

## References
- [Docker —— 从入门到实践](https://yeasy.gitbook.io/docker_practice/compose/django)
