# Django Tastypie

Tastypie是一个web服务api框架,他提供了便捷,强大接口,高度抽象化.

Tastypie可以是models完全开放,但是你可以完全控制你想要开放的东西.

功能特性

- 支持:GET/POST/PUT/DELETE/PATCH
- 合理的配置
- 易扩展
- 包含多种序列化格式(JSON/XML/YAML/bplist)

开始

- 安装 pip install django-tastypie
- 添加到apps中:INSTALLED_APPS += ['tastypie']
- syncdb:./manage.py sysncdb
- 创建资源
- 将资源绑定到urlconf上

配置

唯一强制需要的配置是在INSTALLED_APPS中添加`tastypie`,tastypie的拥有正常的默认配置,并且不是必须的,除非你需要需改他们.详见(tastypie配置)

## 文档

Resources.dispatch_list,调用dispatch
Resources.dispatch,取出Meta中配置的lists_allowed_methods,检查HTTP_X_HTTP_METHOD_OVERRIDE是否改写,request.method检查是否可处理,查找对应的处理方法,如果没有找到就报错,检查是否`is_authenticated`,检查`throttle_check`,调用method,调用`throttle`
Resource.post_list,先deserialize,在alter_deserialized_detail_data,再build_bundle,再obj_create,再get_resource_uri

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

上面建立了新的资源,EntryResource会自动建立所有的非关联字段,这些字段会关联到User的字段上;Meta中的resource_name是可选的,如果没有提供,那么默认为类名去掉Resource之后的小写字符串(`UserResource.__name__[:-8].**class**lower()`).

这个resource_name用来澄清,URL中对应资源名称,这里社否设置无关紧要.

#### 创建关联字段

因为tastypie不知道开发者会如何展示数据,所以没有对关联字段自动进行关联.例如user的group_ids无法进行关联

解决方法:创建一个GroupResource资源

```python
from tastypie import fields
from tastypie.resources import ModelResource
from account.models import User, Group

class GroupResource(ModelResource):
    class Meta:
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
对于当个资源添加url

```python
entry_resource = EntryResource()
urlpatterns = [
    url(r'^api/', include(entry_resource.urls)),
]
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

假设有下面的接口

```python
# myapp/api/resources.py
from django.contrib.auth.models import User
from tastypie.authorization import Authorization
from tastypie import fields
from tastypie.resources import ModelResource, ALL, ALL_WITH_RELATIONS
from myapp.models import Entry


class UserResource(ModelResource):
    class Meta:
        queryset = User.objects.all()
        resource_name = 'user'
        excludes = ['email', 'password', 'is_active', 'is_staff', 'is_superuser']
        filtering = {
            'username': ALL,
            'pub_date': ['exact', 'lt', 'lte', 'gte', 'gt'],

        }


class EntryResource(ModelResource):
    user = fields.ForeignKey(UserResource, 'user')

    class Meta:
        queryset = Entry.objects.all()
        resource_name = 'entry'
        authorization = Authorization()
        filtering = {
            'user': ALL_WITH_RELATIONS,
            'pub_date': ['exact', 'lt', 'lte', 'gte', 'gt'],
        }


# urls.py
from django.conf.urls import url, include
from tastypie.api import Api
from myapp.api.resources import EntryResource, UserResource

v1_api = Api(api_name='v1')
v1_api.register(UserResource())
v1_api.register(EntryResource())

urlpatterns = [
    # The normal jazz here...
    url(r'^blog/', include('myapp.urls')),
    url(r'^api/', include(v1_api.urls)),
]
```

调用c`url http://localhost:8000/api/v1/`返回当前api接口下所有的资源

```javascript
{
    "entry": {
        "list_endpoint": "/api/v1/entry/",
        "schema": "/api/v1/entry/schema/"
    },
    "user": {
        "list_endpoint": "/api/v1/user/",
        "schema": "/api/v1/user/schema/"
    }
}
```

`curl http://localhost:8000/api/v1/?fullschema=true`获取所有信息

```javascript
{
    "entry": {
        "list_endpoint": "/api/v1/entry/",
        "schema": {
            "default_format": "application/json",
            "fields": {
                "body": {
                    "help_text": "Unicode string data. Ex: \"Hello World\"",
                    "nullable": false,
                    "readonly": false,
                    "type": "string"
                },
                ...
            },
            "filtering": {
                "pub_date": ["exact", "lt", "lte", "gte", "gt"],
                "user": 2
            }
        }
    },
}
```

## Tastypie 设置

### API_LIMIT_PER_PAGE 可选 默认20

设置默认情况下列表显示的数据量,这样就不需要使用limit来指定

### TASTYPIE_FULL_DEBUG 可选 默认False

控制异常情况下的处理方式,如果True,而且django的DEBUG=true,那么使用标准django的500.如果False,当DEBUG=True会得到确切的错误信息,当DEBUG=False时,Tasypie会调用`mail_admins()`方法并且提供canned消息(canned可以使用TASTYPIE_CANNED_ERROR来重写)

### TASTYPIE_CANNED_ERROR 可选

例如`TASTYPIE_CANNED_ERROR = "Oops, we broke it!"`

### TASTYPIE_ALLOW_MISSING_SLASH 可选 默认False

允许URL结尾不用`\`,必须和`settings.APPEND_SLASH = False`一起使用这样django就不会触发302

### TASTYPIE_DATETIME_FORMATTING 可选 默认iso-8601\. iso-8601

设置时间的默认格式

### TASTYPIE_DEFAULT_FORMATS 可选 默认['json', 'xml', 'yaml', 'plist']

设置tastypie的可选数据格式,例如:TASTYPIE_DEFAULT_FORMATS = ['json', 'xml']

### TASTYPIE_ABSTRACT_APIKEY 可选 默认False

此设置使ApiKey模型成为抽象基类。 这在多数据库设置中可能是有用的，其中许多数据库都有自己的用户数据表和ApiKeyAuthentication不被使用。 没有此设置，必须在包含用户帐户数据的每个数据库（如django.contrib.auth.models.User生成的Django内置auth_user表）中创建tastypie_apikey表。

## 使用Tastypie做非ORM数据的资源

实际上tastypie都是在`Resource`类中处理请求和返回值.`ModelResource`只是在`Resource`上做了封装来处理ORM数据.我们只需要修改`ModelResource`处理ORM数据的方法,就可来处理非ORM的数据.

## 测试

## 资源

Tastypie的request/response是标准的django行为,例如请求`/api/v1/user/?format=json`:

1. Resource.urls 会被django的url规则检测
2. 在匹配view列表时,`Resource.wrap_view('dispatch_list')`会被调用,它提供了基础的错误处理并且允许返回序列化的错误信息
3. 因为`dispatch_list`传递给了`wrap_view`,所以`Resource.dispatch_list`是下一个被调用的方法.This is a thin wrapper around `Resource.dispatch`
4. `dispatch`做了很多事,她确认一下几件事:

  1. http请求方法在`allowed_methods`里面(`method_check`)
  2. class有有一个方法可以处理请求(`get_list`)
  3. 用户被授权(`is_authenticated`)
  4. 用户没有超出请求次数限制(`throttle_check`)

5. `request`做了api下真正的工作:

  1. 通过`Resource.obj_get_list`获取可用的对象.对于`ModelResource`她使用`ModelResource.build_filters`来执行ORM过滤条件,然后过`ModelResource.get_object_list`来返回`QuerySet`,然后把orm过滤条件应用到`QuerySet`上面.
  2. 然后基于用户的输入来对对象进行排序(`ModelResource.apply_sorting`)
  3. 然后使用`Paginator`进行分页,然后把数据进行序列化.
  4. 页中的每一个对象应用了`full_dehydrate`,这样Tastypie就可以将原始数据转化为,`endpoint`支持的字段
  5. 最终他调用`Resource.create_response`

6. `create_response`是一个快捷方法:

  1. 确认需要返回的数据格式(`Resource.determine_format`)
  2. 将数据按照格式序列化
  3. 使用django的`HttpResponse`(200OK)来返回数据

7. 我们把调用栈交给`dispatch`.还有一个`dispatch`可能做得是保存请求信息来做请求限制(`Resource.log_throttled_access`), 然后返回一个`HttpResponse`,这样django就不会退出

处理其他`endpoint`或者HTTP请求方法的过程与上面这个一致,`<http_method>_<list_or_detail>`.在`PUT/POST`中需要额外调用`hydrate`流程,她用来处理获取用户数据,并将数据转变为用于存储的原始数据

## 为什么使用URI

尽管可以使用`full=True`来将关联字段的所有数据展示,但是默认是使用URI.URI也很容易缓存.

## 访问当前的请求

根据当前的请求来改变行为是非常普遍的需求.事实上在`Resource/ModelResource`中,只要`bundle`可见,就可以用`bundle.request`.

`override/extend`的可以将`bundle`参数传递给他

如果使用`Resource/ModelResource`是`request`不可用,那么会有一个空的`Request`.如果在你的代码中这是一个常见的`pattern/usage`,那么你可能需要为不存在的数据做适配.

## 高级数据准备方式

并不是所有的数据都可以通过`object/model`的属性来获得.可能需要添加一些不在model上存在的数据.这里使用`dehydrate/hydrate`.

### Dehydrate过程

tastypie使用`Dehydrate`过程,将原始的复杂model数据转换成用户可以接受数据结构.这通常是将复杂的数据对象转换成包含简单数据结构的字典.

总体来说就是,获取`bundle.obj`对象并构建`bundle.data`

流程如下:

1. 将数据模型放到`Bundle`实例中,然后传递给多个函数.
2. 遍历资源的所有数据,对`bundle`上的每一个字段执行`Dehydrate`.
3. 当处理每一字段时,查找`dehydrate_<fieldname>`的方法,如果有就调用该方法.将`bundle`对象传递给他
4. 所有字段处理完后,如果`Resource`中有`dehydrate`,那么将完整的`bundle`传递给它.

这个流程的目的是将适合序列化的数据填充到`bundle.data`字典中.除了`alter_*`方法之外,这方法用于控制真正被序列化并且发送给客户端的内容.

### 每个字段的Dehydrate

每个字段有自己的`Dehydrate`方法.如果她知道如何获取数据(例如,指定`attribute`参数),那么他会尝试填充.

返回值会通过字段名插入到`bundle.data`字典中

### dehydrate_FOO

因为不是所有数据都可以直接通过属性来获取(例如,通过函数/查询),不论字段如何产生的,使用这个字段都可以向其中填充数据或消息

这里的`FOO`不是字面意思,他是一个占位符,应该被替换为字段名称

例如:

```python
class MyResource(ModelResource):
    # The ``title`` field is already added to the class by ``ModelResource``
    # and populated off ``Note.title``. But we want allcaps titles...
    class Meta:
        queryset = Note.objects.all()

    def dehydrate_title(self, bundle):
        return bundle.data['title'].upper()

class MyResource(ModelResource):
    # As is, this is just an empty field. Without the ``dehydrate_rating``
    # method, no data would be populated for it.
    rating = fields.FloatField(readonly=True)

    class Meta:
        queryset = Note.objects.all()

    def dehydrate_rating(self, bundle):
        total_score = 0.0

        # Make sure we don't have to worry about "divide by zero" errors.
        if not bundle.obj.rating_set.count():
            return total_score

        # We'll run over all the ``Rating`` objects & calculate an average.
        for rating in bundle.obj.rating_set.all():
            total_score += rating.rating

        return total_score /  bundle.obj.rating_set.count()
    # 这里的返回值会更新bundle.data,不应该直接去修改bundle.data的值
```

### dehydrate

`dehydrate`函数接受完整的`bundle.data`,并且应用所有的数据修改.当一个数据可能依赖于多个数据字段时

```python
class MyResource(ModelResource):
    class Meta:
        queryset = Note.objects.all()

    def dehydrate(self, bundle):
        # Include the request IP in the bundle.
        bundle.data['request_ip'] = bundle.request.META.get('REMOTE_ADDR')
        return bundle
class MyResource(ModelResource):
    class Meta:
        queryset = User.objects.all()
        excludes = ['email', 'password', 'is_staff', 'is_superuser']

    def dehydrate(self, bundle):
        # If they're requesting their own record, add in their email address.
        if bundle.request.user.pk == bundle.obj.pk:
            # Note that there isn't an ``email`` field on the ``Resource``.
            # By this time, it doesn't matter, as the built data will no
            # longer be checked against the fields on the ``Resource``.
            bundle.data['email'] = bundle.obj.email

        return bundle
```

这个函数必须返回`bundle`对象,不管它改变现有数据还是添加一个新的数据.甚至可以删除`bundle.data`中的任何数据

### Hydrate 流程

Tastypie将用户发来的数据进行序列化,并将它转化为可用的数据模型.与`dehydrate`流程相反.

1. 将客户端数据放入`Bundle`对象中,那后交给其他函数.
2. 如果`hydrate`函数存在,就将完整的`bundle`对象传递给他
3. 当处理每一字段时,查找`hydrate_<fieldname>`的方法,如果有就调用该方法,将`bundle`对象传递给他
4. 当所有其他的处理完成时,让每一个字段调用自己的`hydrate`,并把`bundle`对象传递给他么

### hydrate

`hydrate`允许初始化`bundle.obj`

这个函数必须返回`bundle`对象,不管它改变现有数据还是添加一个新的数据.甚至可以删除`bundle.obj`中的任何数据

### hydrate_FOO

客户端传来的数据可能没有直接映射到数据模型上,这个方法,允许接受数据并且尽心修改

```python
class MyResource(ModelResource):
    # The ``title`` field is already added to the class by ``ModelResource``
    # and populated off ``Note.title``. But we want lowercase titles...

    class Meta:
        queryset = Note.objects.all()

    def hydrate_title(self, bundle):
        bundle.data['title'] = bundle.data['title'].lower()
        return bundle
```

每个字段都有自己的`hydrate`方法

## 反向关联关系

Tastypie没有像django一样提供了反向关联关系,当时tastypie使用`ToOneField`和`ToManyField`来

```python
# myapp/api/resources.py
from tastypie import fields
from tastypie.resources import ModelResource
from myapp.models import Note, Comment


class NoteResource(ModelResource):
    comments = fields.ToManyField('myapp.api.resources.CommentResource', 'comments')

    class Meta:
        queryset = Note.objects.all()


class CommentResource(ModelResource):
    note = fields.ToOneField(NoteResource, 'notes')

    class Meta:
        queryset = Comment.objects.all()
```

**和django不同的是Tastypie不可以直接用`'CommentResource'`,即使实在统一个module下**

Tastypie也支持`self-referential`字段

```python
# myapp/api/resources.py
from tastypie import fields
from tastypie.resources import ModelResource
from myapp.models import Note


class NoteResource(ModelResource):
    sub_notes = fields.ToManyField('self', 'notes')

    class Meta:
        queryset = Note.objects.all()
```

## 资源选项(AKA Meta)

内置的Meta类,允许对Resource进行配置

### serializer 默认`tastypie.serializers.Serializer()`

控制Resource应该使用哪一个class来做序列化

### authentication 默认`tastypie.authentication.Authentication()`

控制Resource应该使用哪一个class来做authentication

### authorization 默认 `tastypie.authorization.ReadOnlyAuthorization().`

控制Resource应该使用哪一个class来做authorization

### validation 默认 `tastypie.validation.Validation()`

控制Resource应该使用哪一个class来做validation

### paginator_class 默认 `tastypie.paginator.Paginator`

控制Resource应该使用哪一个class来做paginator_class

### cache 默认 `tastypie.validation.NoCache()`

控制Resource应该使用哪一个class来做cache

### throttle 默认 `tastypie.validation.BaseThrottle()`

控制Resource应该使用哪一个class来做 throttle

### allowed_methods 默认 None

控制Resource可以响应的list和detail请求方法,默认None,这意味着交给`list_allowed_methods`和`detail_allowed_methods`这两个选项.

### list_allowed_methods

控制Resource可以响应的list请求方法,默认`['get', 'post', 'put', 'delete', 'patch']`

### detail_allowed_methods

控制Resource可以响应的detail请求方法,默认`['get', 'post', 'put', 'delete', 'patch']`

### limit

空每次请求返回的结果数目,默认为20或者时`API_LIMIT_PER_PAGE`设置的值

### api_name

当创建资源的urls覆盖资源,默认None

### resource_name

资源名称如果没有提供,则使用类名的小写

### default_format

设置默认的数据格式,默认为json

### filtering

是一个字典,字典的key是用户可以进行过滤的字段,value为用户过滤字段时可以使用的方法

### ordering

设置哪些字段用户可以进行排序

### object_class

设置给Resource提供数据的class,在ModelResource中是queryset就model class

### fields

用户可访问字段的白名单

### excludes

用户可访问字段的黑名单

### include_resource_uri

指当用`get_absolute_url`获取对象时,定是否需要包含额外的字段,默认False

### always_return_data

规定HTTP除了(DELETE)方法外,都要返回序列化数据,默认False

### collection_name

当使用get获取list时,list的名称,默认是`objects`

## 基础过滤

ModelResource提供了基础的Django Orm过滤接口.

```python
from tastypie.constants import ALL, ALL_WITH_RELATIONS
class MyResource(ModelResource):
    class Meta:
        filtering = {
            "slug": ('exact', 'startswith',),
            "title": ALL,
        }
```

上面规定slug字段允许使用`'exact', 'startswith'`两个过滤条件,title有可使用所有过滤条件

## 高级过滤

使用高级的过滤你可能使用`build_filters()`来自定义过滤

```python
from haystack.query import SearchQuerySet

class MyResource(Resource):
    def build_filters(self, filters=None):
        if filters is None:
            filters = {}

        orm_filters = super(MyResource, self).build_filters(filters)

        if "q" in filters:
            sqs = SearchQuerySet().auto_query(filters['q'])

            orm_filters["pk__in"] = [i.pk for i in sqs]

        return orm_filters
```

## 对于不支持使用PUT/DELETE/PATCH的场景

使用`X-HTTP-Method-Override`替换 例如`curl --dump-header - -H "Content-Type: application/json" -H "X-HTTP-Method-Override: PATCH" -X POST --data '{"title": "I Visited Grandma Today"}' http://localhost:8000/api/v1/entry/1/`

## Resource 方法

### Resource.wrap_view(self, view)

包装函数,例如可以更好的处理异常

### Resource.get_response_class_for_exception(self, request, exception)

必须返回django HttpResponse,可以覆盖自定义用于未捕获异常的响应类

### Resource.base_urls

这个Resource应该响应的标准URL 。这些包括默认list,detail，schema和多个endpoit。必须返回一个url列表

### Resource.prepend_urls

一个钩子，用于添加您自己的URL或在默认URL之前进行匹配。用于添加自定义endpoint或覆盖内置的（从base_urls）。

### Resource.urls

Resource的urls,组合base_urls与override_urls.大多数是一个标准的URLconf，这适合于在注册一个Api类时自动使用，或者直接包含在URLconf中

### Resource.determine_format

用于确定所需的格式

### Resource.serialize

对给定的数据进行序列化

### Resource.deserialize

对给定的请求.数据,类型进行反序列化.依赖于请求的`CONTENT_TYPE`头

### Resource.alter_list_data_to_serialize

在数据列表进行序列话并且发送给用户之前进行数据修改

### Resource.alter_detail_data_to_serialize
在数据详情进行序列话并且发送给用户之前进行数据修改

### Resource.alter_deserialized_list_data
在数据列表从用户端接受到并反序列化之前修改数据

### Resource.alter_deserialized_detail_data

在数据详细从用户端接受到并反序列化之前修改数据,在hydration之前很有用

### Resource.dispatch_list
对于数据列表,处理各种HTTP请求方法,依赖于`Resource.dispatch`

### Resource.dispatch_detail

对于数据详情,处理各种HTTP请求方法,依赖于`Resource.dispatch`

### Resource.dispatch
处理通常的操作(allowed HTTP method, authentication, throttling, method lookup)

### Resource.remove_api_resource_names
给出一个来自URLconf的模式匹配字典,如果有移除api_name and/or resource_name

### Resource.method_check
检查请求方法是否允许

### Resource.is_authenticated
检查是否authenticated,它使用Meta设置中的authentication

### throttle_check
检查用户是否应该被限制

### Resource.log_throttled_access
处理用户访问的记录,用于限制用户访问.它使用Meta中的`throttle`

### build_bundle
传递一个对象或者一个数据字典,给后面的dehydrate/hydrate流程使用,如果没有提供对象,使用`Resource._meta.object_class`创建空对象

### get_bundle_detail_data


## Bundle
