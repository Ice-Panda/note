# Python Web开发Django-02

## 课程内容

### 一、ORM是什么

- 数据表
- 面向对象
- 对应关系

### 二、在django中如何构建映射关系

- 构建对应的model
- 构建数据库迁移文件
- 执行迁移

### 三、常用字段

- 普通字段
- 关联字段

### 四、ORM的使用

- 创建数据
- 更新数据
- 删除数据
- 查询数据

### 五、反向查询
- 1:1 一对一
- 1:M 多对一
- N:M 多对多



## 一、ORM是什么

ORM 对象关系映射，用于实现面向对象编程语言里不同类型系统的数据之间的转换。

数据操作请求-->ORM-->数据库(mysql,oracle,mssql,sqlite3...)

### 数据库表

- 表名
- 字段
- 数据记录

### 面向对象

- 类
- 属性
- 对象

### 结合起来

`类<--->表`

`类属性<---->表字段`

`对象<---->数据记录`

> django中通过操作类和对象来实现对数据库表以及记录的操作

## 二、在django中如何使用ORM

构建类与数据表的对应关系：构建model类-->生成迁移文件-->执行迁移

### 构建model

在MyApp下`model.py`中新增类，继承自`django.db.models.Model`

```python
class Student(models.Model):
    pass 
```
这里的Student对应的是一张数据库表。

为Student类添加属性，对应数据表的字段

```python
class ClassRoom(models.Model):
    name= models.CharField(max_length=32)
    boys=models.IntergerField()
    girls=models.IntergerField()
```

数据表字段字段由字段名、字段类型、字段属性组成

`name`-->`字段名`、`model.CharField`-->`字段类型`、`max_length=32`-->`字段属性`

### 配置django使用的数据库

#### 安装mysql数据库

安装mysql数据库：`sudo apt-get install mysql-server`

创将数据库用户`grant all on *.* to 'django'@'%' identified by 'djangopwd';`

创建数据库 `create database django01 character set=utf8;`


#### 在settings.py中修改默认的数据库

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django01',#数据库名
        'USER': 'django',#数据库用户名
        'PASSWORD': 'djangopwd',#数据库用户密码
        'HOST': 'localhost',#数据库地址
        "PORT": '3306'#数据库端口号
    }
}
```

#### 安装连接mysql的python库

`pip install pymysql`

### 迁移数据表

```shell
# 生成迁移文件，生成的迁移文件在MyApp/migrations/下
./manage.py makemigrations MyApp
# 执行迁移
./manage.py migrate
```

## 三、字段

### 普通字段

- CharField：字符串
- IntegerField：整数
- BooleanField：布尔
- TextField：大文本
- DateField：日期
- DateTimeField：日期时间
- FloatField：浮点

### 关联字段
- ForeignKey 多对一
- ManyToManyField 多对多
- OneToOneField 一对一 是特殊的多对一关系

### 字段属性
- null：默认是False，数据库中该字段是否可以为null
- db_index：默认False，表示该字段是否构建索引
- primary_key：默认False，是否主键
- unique：默认False，该字段的值是否不能有重复
- default：字段的默认值
- blank：默认为False，通常使用在char或text类型上，表示是否可以存储`''`，它与null不同，null是数据库级别限制，blank是业务上的限制
- help_text：帮助说明信息

### 使用ORM

django中执行数据库操作需要通过，类的属性objects执行

#### 创建数据

两种方式创建一条数据

```python
obj=ClassRoom()
obj.attr=value
obj.save()
```

```python
ClassRoom.objects.create
```
一次性创建多个对象

创建一个类对象，对象属性赋值，执行保存

#### 数据更新

ClassRoom.objects.filter().update()

obj.attr=value
obj.save()

更新对象属性，执行保存

#### 删除数据

ClassRoom.objects.filter().update()

obj.delete
调用对象的delete方法







