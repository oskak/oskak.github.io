---
title: Django and MySQL (Django ORM)
tags: [Django, MySQL]
---

## Create a MySQL user for Django development
```
mysql> CREATE USER 'django'@'localhost' IDENTIFIED BY 'newpassword';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALL ON * . * TO 'django'@'localhost';
Query OK, 0 rows affected (0.01 sec)
```

## MySQL 配置
Django 默认用的是 `mysqldb` 模块链接 MySQL，但是该模块的兼容性不好。手动改为用 `pymysql` 链接。

`site_back/settings.py`:
```
import pymysql
pymysql.install_as_MySQLdb()

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'site_back',
        'USER':'django',
        'PASSWORD':'newpassword',
        'HOST':'127.0.0.1',
        'PORT':3306,
        'CHARSET':'utf8'
    }
}
```

## Django ORM
关系型数据库的增删改查等操作，需要用到的是 SQL 语言。Django 附带了一个对象关系映射器（简称 ORM），可以将数据库 SQL 映射到面向对象的 Python 中，使得你可以在 Django 中像操作普通对象一样操作数据库。其直观表现就是模型（Model）。

`app/models.py`:
```
class User(models.Model):
    id = models.AutoField(primary_key=True, verbose_name='Primary Key')
    email = models.CharField(max_length=64, verbose_name='Email')
    username = models.CharField(max_length=32, verbose_name='Username')
    password = models.CharField(max_length=64, verbose_name='Password')
```

定义好了模型，数据库中的表并不会神奇的出现，你还需要把模型转化为对数据库的操作，这就是迁移 Migrations。

在执行 `makemigrations` 指令时，Django 不会检查你的数据库，而是根据目前的模型的状态，创建一个操作列表，使项目状态与模型定义保持最新同步：
```
~/site_back  $ python manage.py makemigrations
Migrations for 'app':
  app/migrations/0001_initial.py
    - Create model User
```

```
~/site_back  $ python manage.py migrate       
Operations to perform:
  Apply all migrations: admin, app, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
```

`migrations` 目录下的迁移文件非常重要并且相互依赖，一般情况下不要随意去修改（虽然 Django 允许你手动维护）。通常情况下，对数据库的操作尽可能通过迁移的方式。每个迁移文件记录的仅仅是和上一次的变化，每一次对数据库的操作是高度依赖的。

## Testing
In project's root directory, `test.py`:
```
import django, os

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "site_back.settings")
django.setup()

from app.models import User

u = User(email='abc@abc.abc', username='abc', password='password')
u.save()
```

`app/views.py`:
```
def index(request):
    one_user = User.objects.get(id=1)
    return HttpResponse(one_user) # 'User object (1)'
```

## Debug
(1) Description:
```
RuntimeError: Model class app.models.User doesn't declare an explicit app_label and isn't in an application in INSTALLED_APPS.
```

Note: The app needs to be registered in `INSTALLED_APPS` in `site_back/settings.py`:
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app'
]
```

(2) Description:
```
django.core.exceptions.AppRegistryNotReady: Apps aren't loaded yet.
```

Note: Tried to import a Django model before calling `django.setup()`.

## References
- [关于python：RuntimeError：模型类myapp.models.class没有声明显式的app_label，也不在INSTALLED_APPS中的应用程序中](https://www.codenong.com/61508290/)
- [Django upgrading to 1.9 error "AppRegistryNotReady: Apps aren't loaded yet."](https://stackoverflow.com/questions/34114427/django-upgrading-to-1-9-error-appregistrynotready-apps-arent-loaded-yet/49461944#49461944)
- [Django 知识库：Migrations数据迁移](https://www.dusaiphoto.com/article/96/)
- [Django 连接MySQL数据库 和Django ORM](https://www.cnblogs.com/sddai/p/14325414.html)
- [django Documentation](https://docs.djangoproject.com/en/3.2/topics/db/queries/)
