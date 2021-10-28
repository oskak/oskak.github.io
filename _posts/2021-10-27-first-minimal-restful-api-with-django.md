---
title: First minimal RESTful API with Django
tags: [Django, API]
---

## Design a minimal API
```
# 接口描述：用户列表资源。GET 请求获取用户列表资源
# 接口地址: http://127.0.0.1:8000/api/v1/users
# 请求方式：GET
# 返回参数：JSON 格式用户列表和状态码
```

## Implement a minimal API
`site_back/settings.py`:
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

`app/serializers.py`:
```
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['username', 'email', 'id']
```

`app/urls.py`:
```
urlpatterns = [
    path('users', views.list_users, name='list_users'),
]
```

`site_back/urls.py`:
```
urlpatterns = [
    path('api/v1/', include('app.urls')),
    path('admin/', admin.site.urls),
    path("api/auth/", include("rest_framework.urls", namespace="rest_framework")),
]
```

编写 API 视图。`app/views.py`：
```
@api_view(['GET'])
def list_users(request):
    users = User.objects.all()
    serializer = UserSerializer(users, many=True)
    # return JsonResponse(serializer.data, safe=False)
    return Response(serializer.data)
```

In project's root directory, `test.py`:
```
import django, os

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "site_back.settings")
django.setup()

from app.models import User
from datetime import datetime

User.objects.create(email='def@abc.abc', username='abc', password='password')
```

Verify at `http://localhost:8000/api/v1/users`.

## References
- [以博客为例使用DRF的序列化器和基于函数的视图开发API](https://pythondjango.cn/django/rest-framework/2-first-blog-API/)
