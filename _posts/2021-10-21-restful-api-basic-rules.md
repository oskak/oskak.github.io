---
title: RESTful 规范的 API
tags: [API]
---

## 再谈序列化
API 开发的本质就是各种后端语言的自己的数据类型序列化为通用的可读可传输的数据格式，比如常见的 JSON 类型数据。

与 Django 自带的 `serializers` 类相比，DRF 的序列化器更强大，可以根据模型生成序列化器，还能对客户端发送过来的数据进行验证。

## 什么是符合 RESTful 规范的 API?
### 协议、域名和版本
尽量使用 https 协议，使用专属域名来提供 API 服务。API 版本可以放在 URL 里面：
```
https://api.example.com/v1
https://www.example.com/api/v1 
```

### URI (统一资源标识符)
在 RESTful 架构中，每个网址代表一种资源（Resource），这个网络地址就是 URI（Uniform Resource Identifier），有时也被称为 URL（Uniform Resource Locator）。因为 URI 对应一种资源，所以里面不能有动词，只能有名词。一般来说，数据库中的表都是同种记录的”集合”（Collection），所以 API 中的名词也应该使用复数形式：
```
https://api.example.com/v1/users # 用户列表资源地址
https://api.example.com/v1/users/{id} # 用户id=5对应资源
https://api.example.com/v1/users/{id}/articles # 用户id=5发表的文章列表
```

如果需要对一个用户信息进行编辑或删除，一个传统 Django 开发者可能将 URL 写成如下所示：
```
https://api.example.com/v1/users/{id}/edit/ # 编辑用户
https://api.example.com/v1/users/{id}/delete/ # 删除用户
```

上面 URL 设计其实是不符合 RESTful 规范的。一个 URI 就应该是一个资源，本身不能包含任何动作（action）。REST 的规范是资源的 URI 地址是固定不变的，对同一资源应使用不同的 HTTP 请求方法进行不同的操作，比如常见的增删查改：
```
[POST]    https://api.example.com/v1/users   // 新增
[GET]     https://api.example.com/v1/users/1 // 查询
[PATCH]   https://api.example.com/v1/users/1 // 更新
[PUT]     https://api.example.com/v1/users/1 // 覆盖，全部更新
[DELETE]  https://api.example.com/v1/users/1 // 删除
```

有时候 URL 比较长，可能由多个单词组成，建议使用中划线”-“分割，而不是下划线”_“作为分隔符，另外每个 url 的结尾不能加斜线”/”：
```
https://api.example.com/v1/user-management/users/{id} # 好
https://api.example.com/v1/user_management/users/{id} # 不好
https://api.example.com/v1/user-management/users/{id}/ # 不好
```

### HTTP 请求方法
常用的 HTTP 请求方法有下面五个（括号里是对应的 SQL 命令），每个方法对应一个操作。客户端在消费服务器提供的 API 服务时不仅要指定请求地址，还需指定请求方法：
```
GET（SELECT）：从服务器取出资源（一项或多项）。
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
DELETE（DELETE）：从服务器删除资源。
```

另外还有两个不常用方法 `HEAD` 和 `OPTIONS`，`HEAD` 和 `GET` 本质是一样的，区别在于 `HEAD` 不含有呈现数据，而仅仅是 HTTP 头信息。`OPTIONS` 极少使用，它主要用于获取当前 URL 所支持的方法。

### 过滤 Filter
如果记录数量很多，服务器不可能都将它们返回给用户。符合 RESTful 规范的 API 应该支持过滤。下面是一些常见的通过参数过滤的方式：
```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个姓名排序，以及排序顺序。
?user_type_id=1：指定筛选条件，比如用户类型
```

### 状态码 Status Codes
服务器在处理客户端请求后还应向用户返回响应的状态码和提示信息，常见的有以下一些状态码：
```
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误
```

### Hypermedia API
RESTful API 最好做到 Hypermedia，即返回结果中提供链接，连向其他 API 方法，使得用户不查文档，也知道下一步应该做什么。比如，当用户向 `api.example.com` 的根目录发出请求，会得到这样一个文档：
```
{"link": {
  "rel":   "collection https://www.example.com/zoos",
  "href":  "https://api.example.com/zoos",
  "title": "List of zoos",
  "type":  "application/vnd.yourformat+json"
}}
```

## References
- [为什么要学习Django REST Framework (DRF), 什么是符合RESTful规范的API?](https://pythondjango.cn/django/rest-framework/1-RESTfull-API-why-DRF/)
