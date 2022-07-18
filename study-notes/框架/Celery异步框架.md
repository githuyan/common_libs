## Celery

> Celery是由Python开发的一个简单、灵活、可靠的处理大量任务的分发系统

### celery 是什么以及应用场景

> Celery是由Python开发的一个简单、灵活、可靠的处理大量任务的分发系统，
> 它不仅支持实时处理也支持任务调度。

### celery实现定时任务

```python
#可以在启动任务的时候使用 这是一个延时任务
apply_async(args=(,),callback=func,countdown=60*60)
# 或者
# eta (estimated time of arrival)：指定任务被调度的具体时间，参数类型是 datetime
# 当前 UTC 时间再加 10 秒后执行任务
apply_async(args=(,),callback=func,eta=datetime.utcnow()+timedelta(seconds=10))

# 手动设置的方法
app.conf.beat_schedule = {
    'add-every-30-seconds': {
        'task': 'tasks.add',
        'schedule': 30.0,
        'args': (16, 16)
    },
}
app.conf.timezone = 'UTC'

# 更详细的配置，使用 crontab，需要导入
app.conf.beat_schedule = {
    # Executes every Monday morning at 7:30 a.m.
    'add-every-monday-morning': {
        'task': 'tasks.add',
        'schedule': crontab(hour=7, minute=30, day_of_week=1),
        'args': (16, 16),
    },
}

# 首先启动celery异步队列
celery -A celery_app worker -l info -P eventlet
# 然后发布定时任务
celery -A celery_app beat


# 查看定时任务，在网页中输入localhost:5555，可以看到worker的运行情况
celery -A proj.app_test flower
```

### 结果属性

**state**

> 当前任务状态，和status一模一样

- PENDING 任务等待被执行
- STARTED 任务已经被执行
- RETRY 任务将被重试，可能是因为失败
- FAILURE 任务抛出异常，或者已超过重试限制，result属性包含任务引发的异常
- SUCCESS 任务执行成功，result属性包含任务返回值

**ready()**

> 只有任务已经被执行（已结束） ，返回True，否则返回False

Return `True` if the task has executed.

If the task is still running, pending, or is waiting for retry then `False` is returned.

**可供回调**

`then`(*callback*, *on_error=None*, *weak=False*)

### 应用

```python
>>> from celery import Celery
>>> app = Celery()
>>> app
<Celery __main__:0x100469fd0>
```

```python
>>> @app.task
... def add(x, y):
...     return x + y

>>> add
<@task: __main__.add>

>>> add.name
__main__.add

>>> app.tasks['__main__.add'] # 如果celery不能检测到这个功能属于哪个模块，它使用主模块名来生成任务名的开头。
<@task: __main__.add>
```

### Configration

```python
>>> app.conf.timezone
'Europe/London'
```

```python
>>> app.conf.update(
...     enable_utc=True,
...     timezone='Europe/London',
...)
```

1. 使用导包的方式使用配置

   ```python
   app.config_from_object('celeryconfig')
   # 需要注意的事，当使用这个方法导入配置时，你原有的配置都将被失效，如果想要再增加额外的配置，应该本方法使用之后再添加
   ```

   ```python
   # celeryconfig.py
   
   enable_utc = True
   timezone = 'Europe/London'
   ```

2. 通过真实的模块对象导入

   > 官方不推荐这样做，因为多了解析对象这个步骤
   >
   > Using the name of a module is recommended as this means the module does not need to be serialized when the prefork pool is used. If you’re experiencing configuration problems or pickle errors then please try using the name of a module instead.

   ```python
   from celery import Celery
   
   app = Celery()
   app.config_from_object(celeryconfig)
   ```

3. 使用一个配置类对象

   ```python
   from celery import Celery
   
   app = Celery()
   
   class Config:
       enable_utc = True
       timezone = 'Europe/London'
   
   app.config_from_object(Config)
   ```

```
T.delay(arg, kwarg=value)
```

调用 apply_async 的快捷方式（.delay(_args, *_kwargs)等价于调用 .apply_async(args, kwargs)）。

```
T.apply_async((arg,), {'kwarg': value})
T.apply_async(countdown=10)
```

从现在起, 十秒内执行。

```
T.apply_async(eta=now + timedelta(seconds=10))
```

从现在起十秒内执行，指明使用eta。

```
T.apply_async(countdown=60, expires=120)
```

从现在起一分钟执行，但在两分钟后过期。

```
T.apply_async(expires=now + timedelta(days=2))
```

两天内过期，使用datetime对象。



borker 一个消息队列

backend 用于存储这些消息以及celery执行的一些消息和结果

在 tasks.py 文件所在目录运行

$ celery worker -A tasks.app -l INFO
这个命令会开启一个在前台运行的 worker，解释这个命令的意义：

worker: 运行 worker 模块。

-A: –app=APP, 指定使用的 Celery 实例。

-l: –loglevel=INFO, 指定日志级别，可选：DEBUG, INFO, WARNING, ERROR, CRITICAL, FATAL

其它常用的选项：

-P: –pool=prefork, 并发模型，可选：prefork (默认，multiprocessing), eventlet, gevent, threads.

-c: –concurrency=10, 并发级别，prefork 模型下就是子进程数量，默认等于 CPU 核心数

完整的命令行选项可以这样查看：

$ celery worker --help



后台运行命令

celery multi start w1 -A celery_tasks -l info
1
重启命令

celery multi restart w1 -A celery_tasks -l info
1
立即停止运行(不会等待没有完成的任务完成就停止)

celery multi stop w1 -A celery_tasks -l info
1
安全的停止运行（等待正在进行的任务完成在停止运行）

celery multi stopwait w1 -A celery_tasks -l info



task.delay().then(func)

task.apply_async()

## 坑

1. ImportError: attempted relative import with no known parent package

   **在将任务写入另一个包里，从主包中导入app到此包，然后通过装饰器app.task注册任务**

   ```python
   # celerylearn/tasks.py
   
   app = Celery('mycelery',
                backend='redis://127.0.0.1:6379/1',
                broker='redis://127.0.0.1:6379/0',
                include=['new_task'] # 需要实现在配置文件中写入 include=[]
            )
   # 然后在导包的文件中写入，
   
   from tasks import app # 使用os.getcwd() 查看当前所在地址,已经在celerylearn目录中
   
   imports = [
       'mail' # 这里导入的是任务所在的包
   ]
   
   @app.task
   def start():
       print('成功')
   ```

2. 配置文件的导入

   ```python
   broker_url = 'redis://127.0.0.1:6379/0' # 不要写错了这里直接是URL，没有 celery
   result_backend = 'redis://127.0.0.1:6379/1'
   task_serializer = 'json'
   result_serializer = 'json'
   result_expires = 60 * 60 * 24  # 任务过期时间
   accept_content = ["json"]
   
   # 这是在文件中配置，不知道啥意思
   alternative=f'Use the {_TO_NEW_KEY[setting]} instead')
   ```

   ```python
   # 在Celery6.0 要在命令行单独更新配置文件地址
   # 只会后生成一个 configs.py.orig 文件，以后就不用配置了
   
   # Please run `celery upgrade settings path/to/settings.py` to avoid these warnings and to allow a smoother upgrade to Celery 6.0.
   
   celery upgrade settings configs.py
   ```

3. 当使用celery对接redis的时候，要用127.0.0.1:6379，而不能指定为 localhost

4. celery6.0版本以后将废弃大写的配置方式，使用小写的方式进行配置，并且大写和小写的方式不能混用，否则会报错。

5. 不要太相信pycharm便捷器，他不总是对的，即使导入标红，但是只要原则正确，就是可以导入成功

6. **文件导入问题**

   这是python导入的问题，可以 通过sys.path获取python导包的默认路径，在这些路径上的所有包都可以正常导入，然后获取目标路径 os.path.abspath(os.path.pardir)，将目标路径添加进导包路径中，就可以正常导入

   ```python
   import os,sys
   
   sys.path.append(os.path.abspath(os.path.pardir))
   
   from celery import Celery
   
   celery = Celery(__file__)
   # 这里虽然写法不同，但是导包的原理实现相同的
   celery.config_from_object('setting.celeryconfig')
   ```

7. kombu.exceptions.ContentDisallowed: Refusing to deserialize untrusted content of type pickle (application/
   x-python-serialize) 序列化问题

   ```python
   #
   task_serializer = 'pickle'
   accept_content = ['pickle','json']
   ```

8. AttributeError: Cant pickle local object
