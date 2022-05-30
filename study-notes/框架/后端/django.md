# django

流程

一个主程序，一个应用程序（相当于一个蓝图），在应用程序中写应用，在主程序中注册路由，配置全局变量，

不用装饰器，直接在view中写视图函数，然后再应用程序的urls模块导入注册

## 应用

创建项目，app

```python
django-admin startproject myproject
python manage.py startapp myapp
```

既然要取数据，那model肯定得和数据库的一致，我发现一个快捷的方式可以把数据库中的表生成对应的model，在项目目录下执行命令

```
python manage.py inspectdb
```

数据库

```python
# 创建数据库迁移文件
python manage.py makemigrations polls
# 执行迁移
python manage.py migrate

# 查看迁移具体的sql命令
python manage.py sqlmigrate application 0001
```



```python
# 模板渲染
from django.template import loader

# 获取模板，填入参数
template = loader.get_template('polls/index.html')
return HttpResponse(template.render(context, request))
```



```python
# 这句的意思是在数据库中查找id=1的数据，没有的话返回404错误，
# 相当于查找并判断，这只是一个快捷方式
obj = get_object_or_404(SomeModel, id=1)
```

增删改查

```python
# 查
a = Article.objects.all()
b = Article.objects.get(id=1)
c = Article.objects.filter(id=1)
# 增
a.save()
# 改
models.Book.objects.filter(id=3).update(title="PHP")

a.title = 2
a.save()

Article.objects.filter(id=1).update(user='tom')
# 删
a.delete()
Article.objects.filter(id=2).delete()
```

#### restful API

```python
#获取GET传值参数
name=request.GET.get('name')
age=request.GET.get('age')
department=request.GET.get('dep')
```



