---
title: Django and PostgreSQL (Django ORM)
tags: [Django, PostgreSQL]
---

## Download PostgreSQL
[PostgreSQL Database Download](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)

Confirm the processes running for Postgres:
```
ps -ef | grep postgres
```

On a Mac you should be able to locate `psql` under the `Applications > PostgreSQL (version number) > SQL Shell (psql)`. 

## Setting up PostgreSQL in the Django project
```
pip install psycopg2
pip install psycopg2-binary # otherwise 'Reason: image not found'
```

`site_back/settings.py`:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'site_back',
        'USER': 'postgres',
        'PASSWORD': 'password', 
        'HOST': 'localhost', 
        'PORT': '5433' 
    }
}
```

Test db connection:
```
~/site_back [main] $ python manage.py shell
>>> from django.db import connection
>>> cursor = connection.cursor
```

## Create models and migrate
`app/models.py`:
```
class User(models.Model):
    """
    CharField必须要指定max_length参数 不指定会报错
    verbose_name对字段的解释
    """
    id = models.AutoField(primary_key=True, verbose_name='Primary Key')
    email = models.CharField(max_length=64, verbose_name='Email')
    username = models.CharField(max_length=32, verbose_name='Username')
    password = models.CharField(max_length=64, verbose_name='Password')

```

Create db:
```
postgres=# CREATE DATABASE site_back;
```

Make migration:
```
$ python manage.py makemigrations app

$ python manage.py migrate           
```

Connect to `site_back` database and verify that the `app_user` table is created:
```
postgres=# \c site_back
You are now connected to database "site_back" as user "postgres".
```

```
site_back=# \dt
                   List of relations
 Schema |            Name            | Type  |  Owner   
--------+----------------------------+-------+----------
 public | app_user                   | table | postgres
 public | auth_group                 | table | postgres
 public | auth_group_permissions     | table | postgres
```

## Testing
In project's root directory, `test.py`:
```
import django, os

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "site_back.settings")
django.setup()

from app.models import User

User.objects.create(email='abc@abc.abc', username='abc', password='password')
```

`app/views.py`:
```
def index(request):
    one_user = User.objects.get(id=1)
    return HttpResponse(one_user) # 'User object (1)'
```

## Debug
(1) Description: supposed to have detected some changes but didn't:
```
$ python manage.py makemigrations
No changes detected
```

Note:
```
$ python manage.py makemigrations app    
Migrations for 'app':
  app/migrations/0001_initial.py
    - Create model User
```

## References
- [Connecting PostgreSQL using psql and pgAdmin](https://www.enterprisedb.com/postgres-tutorials/connecting-postgresql-using-psql-and-pgadmin)
- [如何在Django中配置使用PostgreSQL](https://www.cnblogs.com/HouJiao/p/13525176.html)
- [Django - makemigrations - No changes detected](https://stackoverflow.com/questions/36153748/django-makemigrations-no-changes-detected)
