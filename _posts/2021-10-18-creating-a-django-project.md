---
title: Creating a Django project
tags: [Django]
---

## Creating a Django project
Create a project called `site_back`:
```
$ django-admin startproject site_back
```

Let’s look at what `startproject` created:
```
~/site_back  $ tree -I venv 
.
├── manage.py
└── site_back
    ├── __init__.py
    ├── asgi.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

分析文件结构：
- The outer `site_back/` is the container for your project. Its name doesn’t matter to Django; you can rename it to anything you like.
- `manage.py`: A command-line utility that lets you interact with this Django project in various ways.
- The inner `site_back/` directory is the actual Python package for your project. Its name is the Python package name you’ll need to use to import anything inside it (e.g. `site_back.urls`).
- `site_back/__init__.py`: An empty file that tells Python that this directory should be considered a Python package.
- `site_back/settings.py`: Settings/configuration for this Django project. 
- `site_back/urls.py`: The URL declarations for this Django project; a “table of contents” of your Django-powered site.
- `site_back/asgi.py`: An entry-point for ASGI-compatible web servers to serve your project.
- `site_back/wsgi.py`: An entry-point for WSGI-compatible web servers to serve your project.

Start the Django development server:
```
$ python manage.py runserver
```

To create your app, make sure you’re in the same directory as `manage.py` and type this command:
```
$ python manage.py startapp app
```

```
~/site_back  $ tree -I venv                 
.
├── app
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── manage.py
└── site_back
    ├── __init__.py
    ├── asgi.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

## Write your first view
`app/views.py`:
```
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

Create `app/urls.py`:
```
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

The next step is to point the root URLconf at the `app.urls` module. In `site_back/urls.py`, add an import for `django.urls.include` and insert an `include()` in the `urlpatterns` list, so you have:
```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('/', include('app.urls')),
    path('admin/', admin.site.urls),
]
```

## References
- [Writing your first Django app, part 1](https://docs.djangoproject.com/en/3.2/intro/tutorial01/)
