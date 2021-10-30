---
title: 理解 Session - 基于session机制的用户认证
tags: [HTTP, Django]
---

## Session 机制
Web 应用程序中经常使用 Session 来记录客户端状态。Session 是服务器端使用的一种记录客户端状态的机制，使用上比 Cookie 简单一些，相应的也增加了服务器的存储压力。

Session 技术是服务端的解决方案，它是通过服务器来保持状态的。由于 Session 这个词汇包含的语义很多，因此需要在这里明确一下 Session 的含义。首先，我们通常都会把 Session 翻译成会话，因此我们可以把客户端浏览器与服务器之间一系列交互的动作称为一个 Session。从这个语义出发，我们会提到 Session 持续的时间，会提到在 Session 过程中进行了什么操作等等；其次，Session 指的是服务器端为客户端所开辟的存储空间，在其中保存的信息就是用于保持状态。从这个语义出发，我们则会提到往 Session 中存放什么内容，如何根据键值从 Session 中获取匹配的内容等。要使用 Session，第一步当然是创建 Session 了。那么 Session 在何时创建呢？是在服务器端程序运行的过程中创建的，不同语言实现的应用程序有不同创建 Session 的方法。在创建了 Session 的同时，服务器会为该 Session 生成唯一的 `session id`，而这个 `session id` 在随后的请求中会被用来重新获得已经创建的 Session；在 Session 被创建之后，就可以调用 Session 相关的方法往 Session 中增加内容了，而这些内容只会保存在服务器中，发到客户端的只有 `session id`；当客户端再次发送请求的时候，会将这个 `session id` 带上，服务器接受到请求之后就会依据 `session id` 找到相应的 Session，从而再次使用之。正是这样一个过程，用户的状态得以保持。

## Session 生命周期
为了获得更高的存取速度，服务器一般把 Session 放在内存。Session 在用户第一次访问服务器的时候自动创建。只有访问 `JSP`、`Servlet` 等程序时才会创建 Session，只访问 `HTML`、`IMAGE` 等静态资源并不会创建 Session。Session 生成后，只要用户继续访问，服务器就会更新 Session 的最后访问时间，并维护该 Session。用户每访问服务器一次，无论是否读写 Session，服务器都认为该用户的 Session “活跃（active)”了一次。

## 认证与权限的区别
认证(Authentication)与权限(Permission)不是一回事。认证是通过用户提供的用户ID/密码组合或者Token来验证用户的身份。权限(Permission)的校验发生在验证用户身份之后，是由系统根据分配权限确定用户可以访问何种资源以及对这种资源进行何种操作，这个过程也被称为授权(Authorization)。

无论是 Django 还是 DRF，当用户成功通过身份验证以后，系统会把已通过验证的用户对象与 `request` 请求绑定，这样一来就可以使用 `request.user` 获取这个用户对象的所有信息了。

## 给视图添加权限
在 Django 传统视图开发中你可能会使用 `@login_required` 和 `@permission_required` 这样的装饰器要求用户先登录或进行权限验证。在 DRF 中不需要这样做，因为 REST framework 包含许多默认权限类，我们可以用来限制谁可以访问给定的视图。在这种情况下，我们需要的是 `IsAuthenticatedOrReadOnly` 类，它将确保经过身份验证的请求获得读写访问权限，未经身份验证的请求将获得只读的权限。

`app/views.py`:
```
from rest_framework import permissions
from rest_framework import generics

class UserList(generics.ListCreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer

class UserDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
```

`app/urls.py`:
```
urlpatterns = [
    # path('users', views.list_users, name='list_users'),
    path('users', views.UserList.as_view()),
    re_path(r'^users/(?P<pk>[0-9]+)$', views.UserDetail.as_view()),
]
```

`site_back/urls.py`:
```
urlpatterns = [
    path('api/v1/', include('app.urls')),
    path('admin/', admin.site.urls),
    path("api-auth/", include("rest_framework.urls", namespace="rest_framework")),
]
```

此时再访问用户资源列表或单个用户资源时，delete 按钮和添加修改表单都已消失：

<img src="{{ site.baseurl }}/assets/images/django1.png" style="border:1px solid black;">

当你重新登录验证身份后，又可以看到 delete 按钮和修改表单：

<img src="{{ site.baseurl }}/assets/images/django2.png" style="border:1px solid black;">

大多数情况下需要自定义权限，见[认证与权限](https://pythondjango.cn/django/rest-framework/5-permissions/)。

本文的例子使用了默认的基于 session 机制的用户认证。

## References
- [理解Cookie和Session机制，看懂这一篇就能理解原理了](https://blog.csdn.net/zhuxiaoping54532/article/details/105715528)
- [认证与权限](https://pythondjango.cn/django/rest-framework/5-permissions/)
