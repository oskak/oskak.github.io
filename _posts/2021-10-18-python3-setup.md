---
title: Python3 Setup
tags: [Python3]
---

## 创建一个”全局“虚拟环境？
macOS Big Sur 自带 Python3，一般不直接使用这个 Python3 进行开发作业。而创建的虚拟环境移动起来也比较麻烦，见 [Can I move a virtualenv?](https://stackoverflow.com/questions/32407365/can-i-move-a-virtualenv)。可以先创建一个“全局”的虚拟环境：
```
$ python3 -m venv just_my_venv
```

使用这个“全局”的虚拟环境下载必要的库并方便创建初始项目：
```
$ source just_my_venv/bin/activate
$ pip install django
```

```
$ django-admin startproject site_back
```

平时我们也不会使用这个“全局”的虚拟环境进行开发作业，退出：
```
$ deactivate
```

进入 `site_back` 重新创建一个属于应用的虚拟环境：
```
$ cd site_back
$ python3 -m venv venv
```
