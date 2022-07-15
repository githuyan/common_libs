## Tasks

### app.task(**kwargs)

> 传递给任务装饰器的任何关键字参数实际上都将设置为结果任务类的属性，这是内置属性的列表

- **name**

  > 每个处理任务的名称是唯一的，建议使用 模块名+任务名

  ```python
  @app.task(name="celery_app.add")
  def add(): 
      pass
  ```

  

​	



#### 任务继承

> 或许可以用来做状态的收集

```python
import celery

class MyTask(celery.Task):

    def on_failure(self, exc, task_id, args, kwargs, einfo):
        print('{0!r} failed: {1!r}'.format(task_id, exc))

@app.task(base=MyTask)
def add(x, y):
    raise KeyError()
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

