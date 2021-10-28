---
title: 基于函数的视图 vs 基于类的视图 (Django)
tags: [Django]
---

## 编写基于函数的 API 视图 `views.py`
与 Django 一样，DRF 也支持使用基于函数的视图 (Functional Based View, FBV) 和基于类的视图 (Class Based View, CBV) 来编写视图。这里我们使用了 DRF 提供的 `@api_view` 这个非常重要的装饰器，实现了以下功能：
- 与 Django 传统函数视图相区分，强调这是 API 视图，并限定了可以接受的请求方法。
- 拓展了 Django 原来的 `request` 对象。新的 `request` 对象不仅仅支持 `request.POST` 提交的数据，还支持其它请求方式如 `PUT` 或 `PATCH` 等方式提交的数据，所有的数据都在 `request.data` 字典里。

```
@api_view(['GET', 'POST'])
def list_users(request):
    if request.method == 'GET':
        users = User.objects.all()
        serializer = UserSerializer(users, many=True)
        # return JsonResponse(serializer.data, safe=False)
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

## 为什么使用基于类的视图(CBV)?
一个中大型的 Web 项目代码量通常是非常大的，如果全部使用函数视图写，那么代码的复用率是非常低的。使用类视图，可以有效地提高代码复用，因为类是可以被继承的，可以拓展的。特别是将一些可以共用的功能抽象成 `Mixin` 类或基类后可以减少重复造轮子的工作。

## 使用基础 APIView 类
不需要再对用户的请求方法进行判断。视图可以自动将不同请求转发到相应处理方法，逻辑上也更清晰。

`app/views.py`:
```
from rest_framework.views import APIView

class UserList(APIView):
    """
    List all users, or create a new user.
    """
    def get(self, request, format=None):
        users = User.objects.all()
        serializer = UserSerializer(users, many=True)
        return Response(serializer.data)

    def post(self, request, format=None):
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

`app/urls.py`:
```
urlpatterns = [
    # path('users', views.list_users, name='list_users'),
    path('users', views.UserList.as_view()),
]
```

## 用 Mixin 类和 GenericAPI 类混配
使用基础的 `APIView` 类并没有大量简化我们的代码，增删改查操作相关的代码对所有模型几乎都是一样的。对于这些通用的增删改查行为，DRF 已经提供了相应的 `Mixin` 类。`Mixin` 类可与 `generics.GenericAPIView` 类联用，灵活组合成你所需要的视图。`GenericAPIView` 类继承了 `APIView` 类，提供了基础的 API 视图。它对用户请求进行了转发，并对 Django 自带的 `request` 对象进行了封装。不过它比 `APIView` 类更强大，因为它还可以通过 `queryset` 和 `serializer_class` 属性指定需要序列化与反序列化的模型和 `queryset` 所用到的序列化器类。

`app/views.py`:
```
from rest_framework import mixins
from rest_framework import generics

class UserList(mixins.ListModelMixin,
                  mixins.CreateModelMixin,
                  generics.GenericAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
```

## 使用通用视图 `generics.*` 类
将 `Mixin` 类和 `GenericAPIView` 类混配，已经帮助我们减少了一些代码，但我们还可以做得更好。比如将 `get` 请求与 `Mixin` 类提供的 `list` 方法进行绑定感觉有些多余。DRF 还提供了一套常用的已经将 `Mixin` 类与 `GenericAPIView` 类组合好了的视图，开箱即用，可以进一步简化我们的代码。

`app/views.py`:
```
from rest_framework import generics

class UserList(generics.ListCreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

Verify at `http://localhost:8000/api/v1/users`.

## 何时使用 Django 视图集
Django 视图集 (viewset) 代码最少，但这是以牺牲了代码的可读性为代价的，因为它对代码进行了高度的抽象化。另外 urls 由 router 生成，不如自己手动配置的清楚。总结一下：
- 基础的 API 类：可读性最高、代码最多、灵活性最高。当你需要对 API 行为进行个性化定制时，建议使用这种方式。
- 通用 generics 类：可读性好、代码适中、灵活性较高。当你需要对一个模型进行标准的增删查改全部或部分操作时，建议使用这种方式。
- 使用视图集 viewset：可读性较低、代码最少、灵活性最低。当你需要对一个模型进行标准的增删查改的全部操作且不需定制 API 行为时，建议使用这种方式。

## References
- [基于类的视图APIView, GenericAPIView和视图集(ViewSet)](https://pythondjango.cn/django/rest-framework/3-CBV-APIView-viewsets/)
