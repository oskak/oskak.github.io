---
title: 初识 Django REST Framework
tags: [Django]
---

## DRF 预备
```
$ python manage.py createsuperuser --email omega@example.com --username omega
```

Register your apps:
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app',
    'rest_framework',
]
```

## Django 和序列化
前后端分离的核心思想之一，就是两端交互不通过模板语言，而只传输需要的数据。

在 Django 程序的运行过程中，变量都是存储在服务器的内存中；后端 Django 程序中存储的是 Python 变量，而前端的浏览器中是 Javascript 变量。因此需要规定一个“标准格式”，前后端都根据这个标准格式，对资源进行保存、读取、传输等操作。

前后端分离中涉及到一个重要的概念：序列化。DRF 帮我们封装好序列化的底层实现，让开发者专注于业务本身。

举个例子，把 Python 对象转换为 JSON，这被称为序列化（serialization）：
```
>>> import json
>>> person = dict(name='Trump', age=82)
>>> json.dumps(person)
# 这是个字符串
'{"age": 82, "name": "Trump"}'
```

把变量从内存中变成可存储或传输的过程称之为序列化，反之把变量内容从序列化的对象重新读到内存里称之为反序列化。

回顾 Django 传统流程对一个网络请求的处理：视图函数将数据作为上下文返回，通过模板引擎将上下文渲染为页面中的数据。

Restful 的处理流程仅增加了一步，即对数据序列化的处理：数据被序列化为 JSON 字符串，直接交由前端处理。

这就是前后端分离的雏形：后端提供数据；前端专注于操作数据、渲染页面。

## Testing
`app/views.py`:
```
def list_users(request):
    users = User.objects.all()
    serializer = UserSerializer(users, many=True)
    return JsonResponse(serializer.data, safe=False)
```

`app/serializers.py` (序列化器):
```
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['username', 'email', 'id']
```

`app/urls.py`:
```
urlpatterns = [
    path('', views.list_users, name='list_users'), # '[{"username": "abc", "email": "abc@abc.abc", "id": 1}]'
]
```

`site_back/urls.py`:
```
urlpatterns = [
    path('api/users/', include(('app.urls','app'), namespace='users')), # '[{"username": "abc", "email": "abc@abc.abc", "id": 1}]'
    path('app/', include('app.urls')), # '[{"username": "abc", "email": "abc@abc.abc", "id": 1}]'
    path('admin/', admin.site.urls),
]
```

Verify result at `http://localhost:8000/api/users/`.

## Debug
(1) Description and suggestion:
```
raise ImproperlyConfigured(
django.core.exceptions.ImproperlyConfigured: Specifying a namespace in include() without providing an app_name is not supported. Set the app_name attribute in the included module, or pass a 2-tuple containing the list of patterns and app_name instead.
```

```
>>> import django
>>> django.VERSION
(3, 2, 8, 'final', 0)
```

## References
- [Django REST framework Quickstart](https://www.django-rest-framework.org/tutorial/quickstart/)
- [Django-Vue搭建个人博客：初识DRF](https://www.dusaiphoto.com/article/105/)
- [Specifying a namespace...](https://stackoverflow.com/questions/48608894/improperlyconfigurederror-about-app-name-when-using-namespace-in-include)
