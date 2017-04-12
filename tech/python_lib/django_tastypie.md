# Django Tastypie

Tastypie是一个web服务api框架,他提供了便捷,强大接口,高度抽象化.

Tastypie可以是models完全开放,但是你可以完全控制你想要开放的东西.

功能特性

-   支持:GET/POST/PUT/DELETE/PATCH
-   合理的配置
-   易扩展
-   包含多种序列化格式(JSON/XML/YAML/bplist)

开始

-   安装 pip install django-tastypie
-   添加到apps中:INSTALLED_APPS += ['tastypie']
-   syncdb:./manage.py sysncdb
-   创建资源
-   将资源绑定到urlconf上

配置

唯一强制需要的配置是在INSTALLED_APPS中添加`tastypie`,tastypie的拥有正常的默认配置,并且不是必须的,除非你需要需改他们.详见(tastypie配置)

## 文档

### 开始

#### 创建资源

这里我们在Api/api.py创建一个资源

```python
from tastypie.resources import ModelResource
from account.models import User

class UserResource(ModelResource):
    class Meta:
        queryset = User.objects.all()
        resource_name = 'user'
        authorization = Authorization()
```
上面建立了新的资源,EntryResource会自动建立所有的字段,这些字段会关联到User的字段上;Meta中的resource_name是可选的,如果没有提供,那么默认为类名去掉Resource之后的小写字符串(`UserResource.__name__[:-8].__class__lower()`).

这个resource_name用来澄清,URL中对应资源名称,这里社否设置无关紧要.

#### 创建关联字段
因为tastypie不知道开发者会如何展示数据,所以没有对关联字段自动进行关联.例如user的group_ids无法进行关联

解决方法:创建一个GroupResource资源
```python
from tastypie import fields
from tastypie.resources import ModelResource
from account.models import User, Group

class GroupResource(Resource):
    queryset = User.objects.all()
    resource_name = 'user'

class UserResource(ModelResource):
    group_ids=fields.Many2Many(GroupResource,'group_ids')
    # 这里的第一个group_ids是tastypie的字段名,第二个group_ids是告诉tastypie,User表使用group_ids关联到GroupResource.
    # 这里的两个名字不必相同
    class Meta:
        queryset = User.objects.all()
        resource_name = 'user'
        authorization = Authorization()
```

#### 将资源通过api暴露出来

```python
from django.conf.urls import url, include
from tastypie.api import Api
from myapp.api import EntryResource, UserResource

v1_api = Api(api_name='v1')
v1_api.register(UserResource())
v1_api.register(EntryResource())

urlpatterns = [
    url(r'^api/', include(v1_api.urls)),
]
```
在外部会看到api/v1/`resource_name`/的资源URL

#### 限制数据访问

禁止访问的字段,这里是使用黑名单禁止.
```python
class UserResource(ModelResource):
    class Meta:
        queryset = User.objects.all()
        resource_name = 'user'
        excludes = ['email', 'password', 'is_active', 'is_staff', 'is_superuser']
```
还可以使用白名单来允许.
```python
class UserResource(ModelResource):
    class Meta:
        queryset = User.objects.all()
        resource_name = 'user'
        fields = ['username', 'first_name', 'last_name', 'last_login']
```
限制访问方式
```python
class UserResource(ModelResource):
    class Meta:
        queryset = User.objects.all()
        resource_name = 'user'
        excludes = ['email', 'password', 'is_active', 'is_staff', 'is_superuser']
        allowed_methods = ['get']
```

### 与API交互
