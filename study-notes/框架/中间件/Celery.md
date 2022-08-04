## Tasks

> 任务不是为每个请求实例化的，而是作为全局实例在任务注册表中注册。
> 这意味着每个进程只会调用\__init__构造函数一次，而且任务类在语义上更接近于Actor。
>
> 一个任务函数被定义后，以后的所有任务使用的都是同一个任务实例，所有的sel.xxx 对属性做的修改都会直接反应到整个任务，对之后的任务产生影响

### app.task(**kwargs)

> 传递给任务装饰器的任何关键字参数实际上都将设置为结果任务类的属性，这是内置属性的列表

- **name**

  > 每个处理任务的名称是唯一的，建议使用 模块名+任务名

  ```python
  @app.task(name="celery_app.add")
  def add(): 
      pass
  ```

  
  

##### 任务的属性

**实例：**

```python
@celery_instance.task(bind=True, name="parse.bean_parse_tasks.base_bean_parse",
                      max_retries=3, retry_backoff=1,
                      autoretry_for=[ZeroDivisionError])
def base_bean_parse(self, countdown):
    # todo
    print(self.request)
    print(self.__dict__)
    print("success----")
    import asyncio
    try:
        return asyncio.run(yprint())
    except ZeroDivisionError as exc:
        print('-----retry--------')
        print(id(self))
        self.retry(exc=exc, countdown=countdown)
        
base_bean_parse.delay(5)
```

**self.request**

```json
{"lang": "py",
     "task": "parse.bean_parse_tasks.base_bean_parse",
     "id": "cc183a38-0268-4f50-b08d-c8e58ce4dbfd",
     "shadow": null,
     "eta": "2022-07-27T19:59:54.759759+02:00",
     "expires": null,
     "group": null,
     "group_index": null,
     "retries": 3,
     "timelimit": [null, null],
     "root_id": "cc183a38-0268-4f50-b08d-c8e58ce4dbfd",
     "parent_id": "cc183a38-0268-4f50-b08d-c8e58ce4dbfd",
     "argsrepr": "(5,)",
     "kwargsrepr": "{}",
     "origin": "gen820@DESKTOP-11VL9CJ",
     "ignore_result": false,
     "properties": {"correlation_id": "cc183a38-0268-4f50-b08d-c8e58ce4dbfd",
                    "reply_to": "abcad50d-8ab8-3412-aba7-b6d4d46d554d",
                    "replaced_task_nesting": 0,
                    "origin": "gen820@DESKTOP-11VL9CJ",
                    "delivery_mode": 2,
                    "delivery_info": {"exchange": "",
                                      "routing_key": "parse"},
                    "priority": 0,
                    "body_encoding": "base64",
                    "delivery_tag": "53945d12-891e-436a-8d36-58672c28a87d"},
     "reply_to": "abcad50d-8ab8-3412-aba7-b6d4d46d554d",
     "correlation_id": "cc183a38-0268-4f50-b08d-c8e58ce4dbfd",
     "hostname": "celery@DESKTOP-11VL9CJ",
     "delivery_info": {"exchange": "",
                       "routing_key": "parse",
                       "priority": 0,
                       "redelivered": null},
     "args": [5],
     "kwargs": {},
     "is_eager": false,
     "callbacks": null,
     "errbacks": null,
     "chain": null,
     "chord": null,
     "called_directly": false,
     "_protected": 1}
```

**self.\__dict__** 

```json
{
    '__qualname__': 'base_bean_parse',
     '_orig_run': <bound method base_bean_parse of <@task: parse.bean_parse_tasks.base_bean_parse of /mnt/f/Projects/dispatcher/dispatcher/celery_work/celery_app.py at 0x7f816165db70>>,
     'run': <function base_bean_parse at 0x7f81611d49d8>,
     '__trace__': <function build_tracer.<locals>.trace_task at 0x7f8160e8e730>,
     '_exec_options': {'queue': None,
     'routing_key': None,
     'exchange': None,
     'priority': None,
     'expires': None,
     'serializer': 'json',
     'delivery_mode': None,
     'compression': None,
     'time_limit': None,
     'soft_time_limit': None,
     'immediate': None,
     'mandatory': None}
}
```

##### 任务方法

**self.retry**

```python
self.retry(args=None, kwargs=None, exc=exc, throw=True, eta=None, countdown=None, max_retries=None, **options)
args: tuple # 这个参数就是任务函数的参数
kwargs: Dict #
exc: Exception # 在重试失败后的报错信息
throw: bool=False # 默认为false,标记此任务正在重试，保证在任务执行失败被重新扔进队列时，不会被其他职程捕获
eta: Datetime # 明确指定下次重试时间
count_down: int # 重试延时秒数
max_retries: # 这个任务的最大重试次数，这里的赋值不会影响到整个任务对象的默认设置，如果设置为 None,则这个任务会一直重试

# max_retries 的坑
# max_retries定义的最后一次访问结束后，依然会进入exception 的捕获，依然会执行self.retry 的命令，如果在上一次调用的时候使用 args 或者 kwargs 修改了参数，就能避开 max_retries 的设置
@celery_instance.task(bind=True, name="parse.bean_parse_tasks.base_bean_parse",
                      max_retries=3, retry_backoff=1,
                      autoretry_for=[ZeroDivisionError])
def base_bean_parse(self, countdown):
    # todo
    print("success----")
    print(countdown)
    import asyncio
    try:
        return asyncio.run(yprint())
    except ZeroDivisionError as exc:
        print('-----retry--------')
        print(id(self))
        if countdown == 1:
            self.retry(exc=TypeError('改变重试次数'), countdown=countdown, args=(countdown,), max_retries=1)  # 
            self.retry(exc=TypeError('改变重试次数'), countdown=countdown, args=(countdown+1,), max_retries=1)  # 这样就能避开max_retries=1
        else:
            self.retry(exc=TypeError('自定义错误信息'), countdown=countdown, args=(countdown+1, ))
```



自定义任务

> 自定义任务和普通任务没什么不同
>
> celery继承的类 可以没有 run 函数，也可以有 \__init__ 初始化函数，但是这个函数只会被执行一次，以后的所有任务都使用同一个任务实例，

```python
import celery

class MyTask(celery.Task):
    def __init__(self):
        pass  # 这个初始化函数可以自定义一些全局的变量，

    def on_failure(self, exc, task_id, args, kwargs, einfo):
        print('{0!r} failed: {1!r}'.format(task_id, exc))
        
    def on_retry(self, ...):   # 甚至可以不叫这个名字，只是官方定义更加方便而已
        pass

# 这里的 base 原本默认是 base=Task, 继承后只是将他指定到新的类而已，所有的操作和之前没什么不同，只是其中定义的几个方法依然需要我们手动调用
@app.task(base=MyTask)
def add(x, y):
    if True:
        ...  # 没有 self 怎么运行呢？
    raise KeyError() # 当调用 raise 抛出错误时，会被on_failure捕获，就像一个 try...except 一样
```

**钩子函数**

```python
class BaseDispatchTask(Task):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        print('---------self.__init__--------')
        self.aaa = 3333
        print(self.max_retries)
        self.max_retries = 6

    def on_success(self, retval, task_id, args, kwargs):
        print(task_id)
        print('-----on_success------')
        pass

    def on_failure(self, exc, task_id, args, kwargs, einfo):
        print('-----on_failure------')
        pass

    def on_retry(self, exc, task_id, args, kwargs, einfo):  # fixme 重试任务需要与重试规则匹配
        # retry_await_second = data_block.get("retry_time_interval")  # fixme 延时时间
        # self.retry(exc=exc, cutdown=retry_await_second)
        print('----retry-------')
        pass

    # 这两个钩子函数确实实打实可以用的
    def before_start(self, task_id, args, kwargs):
        print('-----------before_start-------')
        pass

    def after_return(self, status, retval, task_id, args, kwargs, einfo):
        print('--------after_return------')
        pass
```





#### 已知异常的自动重试

> 关于 drainor 的异常处理

```python
@app.task(autoretry_for=(FailWhaleError,), retry_kwargs={'max_retries': 5})
def refresh_timeline(user):
    return twitter.refresh_timeline(user)	

# 任何错误都自动重试
@app.task(autoretry_for=(Exception,))
def x():
    ...
    
# 避免大量任务同时自动重试（一定的随机抖动）
from requests.exceptions import RequestException

@app.task(autoretry_for=(RequestException,), retry_backoff=True)
def x():
    ...
    
# 继承处理
class BaseTaskWithRetry(Task):
    autoretry_for = (TypeError,)
    max_retries = 5
    retry_backoff = True
    retry_backoff_max = 700
    retry_jitter = False
```

- **autoretry_for**  --  **list | tuple**

  > 异常类的列表/元组。如果在任务执行过程中出现任何这些异常，任务将自动重试。默认情况下，不会自动重试任何异常。

- **max_retries**   --  **int**

  > 放弃前的最大重试次数。值`None` 意味着任务将永远重试。默认为3	

- **retry_backoff**   --  **int | bool**

  > 随机抖动
  >
  > 布尔值或数字。如果此选项设置为`True`，自动重试将按照抖动规则延迟。第一次重试延迟 1 秒，第二次重试延迟 2 秒，第三次延迟 4 秒，第四次延迟 8 秒，以此类推。（但是，如果启用，则此延迟值由 修改 [`retry_jitter`](https://docs.celeryq.dev/en/stable/userguide/tasks.html#Task.retry_jitter)。）
  >
  > 如果此选项设置为数字，则将其用作延迟因子。例如，如果将此选项设置为`3`，则第一次重试延迟 3 秒，第二次延迟 6 秒，第三次延迟 12 秒，第四次延迟 24 秒，以此类推。默认情况下，此选项设置为`False`，并且不会延迟自动重试。

  - **retry_backoff_max**   --  **int**

    > 如果`retry_backoff`启用，此选项将设置任务自动重试之间的最大延迟（以秒为单位）。默认情况下，此选项设置`600`为 10 分钟。

- **retry_jitter** -- **bool**

  > 一个布尔值。[抖动](https://en.wikipedia.org/wiki/Jitter)用于将随机性引入指数退避延迟，以防止队列中的所有任务同时执行。如果此选项设置为`True`，则计算的延迟值[`retry_backoff`](https://docs.celeryq.dev/en/stable/userguide/tasks.html#Task.retry_backoff)被视为最大值，实际延迟值将是介于零和该最大值之间的随机数。默认情况下，此选项设置为`True`。

#### 更新任务状态

> 使用 update_state 方法自定义任务状态

[`update_state()`](https://docs.celeryq.dev/en/stable/reference/celery.app.task.html#celery.app.task.Task.update_state)



#### 链式调用

```python
def update_page_info(url):
    # fetch_page -> parse_page -> store_page
    chain = fetch_page.s(url) | parse_page.s() | store_page_info.s(url)
    chain()

@app.task()
def fetch_page(url):
    return myhttplib.get(url)

@app.task()
def parse_page(page):
    return myparser.parse_document(page)

@app.task(ignore_result=True)
def store_page_info(info, url):
    PageInfo.objects.create(url=url, info=info)
    
# 或者
add.apply_async((2, 2), link=[add.s(16), other_task.s()])  # link 支持一个列表
```



### 细节

1. 过期任务

