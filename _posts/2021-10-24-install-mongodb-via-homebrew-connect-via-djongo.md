---
title: Install MongoDB via Homebrew, Connect via Djongo
tags: [MongoDB, Homebrew, Django]
---

## Download MongoDB
[Download MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

Tap the MongoDB Homebrew Tap to download the official Homebrew formula for MongoDB and the Database Tools, by running the following command in your macOS Terminal:
```
brew tap mongodb/brew
```

To install MongoDB, run the following command in your macOS Terminal application:
```
brew install mongodb-community@5.0
```

启动服务：
```
$ brew services start mongodb-community@5.0 
==> Successfully started `mongodb-community` (label: homebrew.mxcl.mongodb-community)
```

## Djongo
Djongo is an improvement over PyMongo in that developers need not write lengthy queries. It maps Python objects to MongoDB documents, i.e., Object Document Mapping (ODM). Djongo ensures that only clean data enters the database. By performing integrity checks, applying validations, etc. with Djongo, there is no need to modify the existing Django ORM. Also see [nesdis/djongo](https://github.com/nesdis/djongo).

## Testing
`site_back/settings.py`:
```
DATABASES = {
       'default': {
            'ENGINE': 'djongo',
            'NAME': 'site_back',
            'CLIENT': {
                'host': 'localhost',
            }
        }
    }
```

`app/models.py`:
```
class User(models.Model):
    """
    CharField必须要指定max_length参数 不指定会报错
    verbose_name对字段的解释
    """
    id = models.BigAutoField(primary_key=True, verbose_name='Primary Key')
    email = models.CharField(max_length=64, verbose_name='Email')
    username = models.CharField(max_length=32, verbose_name='Username')
    password = models.CharField(max_length=64, verbose_name='Password')
```

```
$ python3 manage.py makemigrations
$ python3 manage.py migrate
```

In project's root directory, `test.py`:
```
import django, os

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "site_back.settings")
django.setup()

from app.models import User

u = User()

u.username = 'def'
u.email = 'def@def.def'
u.passowrd = 'password'

u.save()
```

Verify in MongoDB:
```
> db.app_user.find() 
{ "_id" : ObjectId("61760d3e4b09f9e4e4888e23"), "id" : 1, "email" : "def@def.def", "username" : "def", "password" : "<ignored>" }
```

## An introduction to Homebrew
Homebrew installs packages to their own directory and then symlinks their files into `/usr/local`.

```
$ cd /usr/local
$ find Cellar
Cellar/wget/1.16.1
Cellar/wget/1.16.1/bin/wget
Cellar/wget/1.16.1/share/man/man1/wget.1

$ ls -l bin
bin/wget -> ../Cellar/wget/1.16.1/bin/wget
```

### 常用指令
```
$ brew search git #查询可用包
$ brew list #查看已安装包列表
$ brew info mongodb-community #查看任意包信息
```

## References
- [How to Use Django with MongoDB](https://www.mongodb.com/compatibility/mongodb-and-django)
- [Install MongoDB Community Edition on macOS](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)
- [Homebrew](https://brew.sh)
