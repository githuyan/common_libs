# Flask

> Flask用Python语言编写的 轻量级Web开发框架(只提供Web框架的核心功能,自由灵活、方便高度定制) .
>
> 实现功能:  主要实现了路由分发和模板渲染功能,提供了非常好的扩展机制

### Nginx，WSGI，Flask 之间的关系。

Nginx：Hey，WSGI，我刚收到了一个请求，我需要你作些准备，然后由Flask来处理这个请求。
WSGI：OK，Nginx。我会设置好环境变量，然后将这个请求传递给Flask处理。
Flask：Thanks WSGI！给我一些时间，我将会把请求的响应返回给你。
WSGI：Alright，那我等你。
Flask：Okay，我完成了，这里是请求的响应结果，请求把结果传递给Nginx。
WSGI：Good job！Nginx，这里是响应结果，已经按照要求给你传递回来了。
Nginx：Cool，我收到了，我把响应结果返回给客户端。大家合作愉快~

![2019082813555437](D:\桌面\学习笔记\resource\2019082813555437.png)

nginx 和 uWSGI 应该是平级的，但是nginx的动态处理能力不强，所以转发给uWSGI处理

### 路由

注册路由 在 Flask 应用中,路由是指用户请求的URL与视图函数之间的映射。 Flask框架 根据HTTP请求的URL在路由表中匹配预定义的URL规则,找到对应的视图函数, 并将视图函数的执行结果返回WSGI服务器

###  简述Flask上下文管理流程?

1. 当有一个请求进来，会先调用flask的`__call__`方法

   当调用app = Flask(_name_)的时候，创建了程序应用对象app；

   request 在每次http请求发生时，WSGI server调用Flask.call()；然后在Flask内部创建的request对象；，

   app的生命周期大于request和g，一个app存活期间，可能发生多次http请求，所以就会有多个request和g。

2. 这个方法会调用wsgi_app，

3. wsgi_app会实例化两个方法，   

   request和session都属于请求上下文对象。

   - request：封装了HTTP请求的内容，针对的是http请求

   - session：用来记录请求会话中的信息，针对的是用户信息。

   current_app和g都属于应用上下文对象。

   - current_app:表示当前运行程序文件的程序实例。
   - g:处理请求时，用于临时存储的对象，每次请求都会重设这个变量

   ```python
   # 请求上下文和应用上下文，请求上下文传入请求对象，和session对象，应用上下文传入app实例和g对象
   
   ctx = RequestContext(request,session)
   app_ctx = AppContext(app,g)
   ```

   请求上下文：保存了客户端和服务器交互的数据。

   应用上下文：在flask程序运行过程中，保存的一些配置信息，比如程序文件名、数据库的连接、用户信息等

   在对请求上下文做了一些封装之后

   应用上下文和请求上下文会被分别push进对应的栈_app_ctx_stack = LocalStack() ， _request_ctx_stack = LocalStack()，这两个栈使用push(),top(),pop()方法维护

4. 然后此时会执行 before_request钩子函数

5. 然后调用视图函数

   ```python
   # 分别调用偏函数，返回栈中的对象
   
   # context locals
   _request_ctx_stack = LocalStack()
   _app_ctx_stack = LocalStack()
   current_app = LocalProxy(_find_app)
   request = LocalProxy(partial(_lookup_req_object, "request"))
   session = LocalProxy(partial(_lookup_req_object, "session"))
   g = LocalProxy(partial(_lookup_app_object, "g"))
   ```

6. 视图函数处理完之后，会调用pop()方法，将本地`__storage__`中的进程号删掉

7. 然后会执行 after_request钩子函数

8. 返回到浏览器

### Flask中的g的作用？

global

g对象是专门用来来保存用户数据的；

.g对象在一次请求中的所有的代码的地方，都是可以使用的

前面讲到如果在一个请求期间共享数据，可以使用`g`变量，但如果要在不同的请求(`request`)之间共享数据，那就需要使用`session`

### g和全局对象有什么区别？

每个请求进来会为每个请求在Local中建立一个独立空间，也就是在应用上下文的Local对象中建立了一个g对象，当请求走的时候就会删除，所以g的生命周期是一次请求的进来到离开

　　全局变量,是在项目启动创建的，直到项目停止才会销毁
　　无论多少请求进来都可以访问全局变量
　　而我们的g对象一般情况用于before_request中设置值,只为这一次请求建立全局变量

### session的实现机制：

  　　1. 请求进来获取cookie的值
    　　2. 解密cookie转换成字典(没有cookie就是空字典)赋值给ctx.session
      　　3. 当请求走的时候把session的数据加密
        　　4. 设置cookie

### DJango和Flask有什么区别

flask走的是小而精的路线，这是一个python语言编写的轻量级的web开发框架，他只实现了Web服务器最核心的功能，flask虽然只带有路由分发和模板渲染的能力，但他的高质量的第三方插件数量众多，几乎可以实现任何功能，它的可扩展性极强，具有灵活方便的特性

Django做的是大而全的路线，它的功能齐全，适合做大型网站的开发，但他对非关系型数据库的支持要差于Flask

### 如何在Flask中访问会话?

```python
一个会话基本上允许记住从一个请求到另一个请求的信息。在Flask中，它使用签名的cookie，以便用户可以查看会话内容并进行修改。用户可以修改会话，只要它有密钥Flask.secret_key。

会话（seesion）会话数据存储在服务器上。 会话是客户端登录到服务器并注销的时间间隔。 需要在此会话中进行的数据存储在服务器上的临时目录中。
from flask import session导入会话对象
session[‘name’] = ‘admin’给会话添加变量
session.pop(‘username’, None)删除会话的变量
```

## 第三方组件

### 解释Python Flask中的数据库连接?

　　python中的数据库连接有两种方式:

　　在脚本中以用第三方库正常连接,用sql语句正常操作数据库,如mysql关系型数据库的pymsql库
　　用ORM来进行数据库连接,flask中典型的flask_sqlalchemy,已面向对象的方式进行数据库的连接与操作

### SQLAlchemy如何执行原生SQL？

```python
# 查询
cursor = session.execute('select * from users')
result = cursor.fetchall()

# 添加
cursor = session.execute('insert into users(name) values(:value)', params={"value": 'abc'})
session.commit()
print(cursor.lastrowid)
```

### wtforms组件的作用?

```undefined
WTForms是一个支持多个web框架的form组件，主要用于对用户请求数据进行验证。
```

### Flask-WTF是什么,有什么特点

在flask内部并没有提供全面的表单验证，所以当我们不借助第三方插件来处理时候代码会显得混乱，而官方推荐的一个表单验证插件就是wtforms。wtfroms是一个支持多种web框架的form组件，主要用于对用户请求数据的进行验证。

### Flask脚本的常用方式是什么?

　　在shell中运行脚本文件
　　在python编译器中run

### 列举使用过的Flask第三方组件?

　　flask_bootstrap
　　flask-WTF
　　flask_sqlalchemy 
