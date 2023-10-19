# FastApi


## 技巧

### 使路径参数固定, 固定 name 的取值

> http://xxx/name/{name}

```python 
# 使用 python 的 Enum（枚举类）
from enum import Enum

class Name(Enum):
    tom: str = tom
    jerry: str = jerry
@app.get('/name/{Name}')
async def get_name(name: Name):
    if name == Name:
        xxx
```



### 参数校验

1. 查询参数的校验

   > Query()

   ```python
   # 可选且限制长度
   async def get_name(name: Union[str, int] = Query(default=None, max_length=10)):
       min_langth, max_length
       regex="^aaa.*b$"
       ... # 必选参数 Query(..., max_lenght=10)
       default=['tom', 'jerry'] # 默认值也可以是列表
       
   # 	请求体参数客户端必须以 json 的方式发送
   class Name(BaseModel):
       name: str
   async def get_name(name: Name):
   
   # 前端发
   {
       "name": "tom"
   }
   ```




### 依赖项

> 类似于 装饰器，更灵活的中间件，作用于请求之前

**参考：**

- [子依赖项 - FastAPI (tiangolo.com)- 官方文档 --极好](https://fastapi.tiangolo.com/zh/tutorial/dependencies/sub-dependencies/) 
- [ 超全面整理fastAPI(从入门到运用)_fastapi](https://blog.csdn.net/my_name_is_learn/article/details/109819127)

```python
# 依赖项可以是一个函数，一个类，可以任意深度嵌套
async def one(name):
async def two(name: str = Depends(one)):
    return "str"
    
async def metho(o: str = Depends(two)):
```

**公用依赖项**-**缓存**

> 同一个路径操作中多个依赖项公用一个依赖项，fastapi 默认会使用这个依赖项的的缓存， 

```python
# 禁用缓存
async def needy_dependency(fresh_value: str = Depends(get_value, use_cache=False)):
```

**路径操作依赖项**

```python
# 路径操作装饰器依赖项（以下简称为“路径装饰器依赖项”）的执行或解析方式和普通依赖项一样，但就算这些依赖项会返回值，它们的值也不会传递给路径操作函数。
@app.get("/items/", dependencies=[Depends(verify_token), Depends(verify_key)])
async def read_items():
```

**全局依赖项**

> 在系统开启时， 或者每一个请求时都会触发依赖
>
> 或者为每一个路由定义特定的依赖

```python
app = FastAPI(dependencies=[Depends(verify_token), Depends(verify_key)])
```

**嵌套依赖**

> 一个依赖项依赖着另一个依赖项，执行顺序总是最里层的依赖项先被执行
>
> fastapi 只会为同一个路由加载一次依赖项，然后将这个结果缓存，即使这个依赖项在本路由的多处被调用，使用的是第一次的缓存值，通过参数，user_cache=False，禁用缓存

```python
async def needy_dependency(fresh_value: str = Depends(get_value, use_cache=False)):
    return {"fresh_value": fresh_value}
```

**没有返回值的依赖项**

> dependencies 是一个依赖项的列表，仅会启动依赖项，而不会承接此依赖项的任何返回值

```python
async def test(dependencies=[Depends(depend1),Depends(depend2)])
```

**全局依赖项**

```python
from fastapi import Depends, FastAPI, Header, HTTPException


async def verify_token(x_token: str = Header(...)):
    pass

async def verify_key(x_key: str = Header(...)):
    pass

app = FastAPI(dependencies=[Depends(verify_token), Depends(verify_key)])
```

### 中间件（钩子函数）

> 中间件就是一个全局的钩子， 作用于每个请求之前，感觉像是全局依赖项

**参考：**

- [FastApi开发之自定义中间件 | 菜鸟童靴 (boyyongxin.github.io)](https://boyyongxin.github.io/2022/03/30/FastApi开发之自定义中间件/)

```python
# 自定义中间件
from starlette.middleware.base import BaseHTTPMiddleware  # 这是一个ASGI标准的中间件抽象类

class CustomHeaderMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request, call_next):  # 继承后必须重写dispatch方法
        response = await call_next(request)
        response.headers['Custom'] = 'Example'
        return response

app.add_middleware(CustomHeaderMiddleware, "描述性信息，可为空")  # 这里会在starlette的user_middleware列表中注册（追加）一个中间件，

# 这种方式仅仅支持 http 中间件（就是一个例子，无法自定义）
@app.middleware("http") 
def new_middleware():
    pass
```



### 背景任务

> 回调, BackgroundTask

```python
async def btask(name: str):
    
async def router(message: str, backgroundtasks: BackgroundTasks):
    backgroundtasks.add_task(btask, name="tom")
```

### 响应

> fastapi 不会对响应做任何的修改，所以甚至可以直接返回 html 页面

```python 
from fastapi.response import JobResponse

# Response 类接受如下参数：
    content - 一个 str 或者 bytes。
    status_code - 一个 int 类型的 HTTP 状态码。
    headers - 一个由字符串组成的 dict。
    media_type - 一个给出媒体类型的 str，比如 "text/html"。

# 返回html
async def read_items():
    return """
    <html>
        <head>
            <title>Some HTML in here</title>
        </head>
        <body>
            <h1>Look ma! HTML!</h1>
        </body>
    </html>
    """
```

#### 流式响应





### 安全性

### 跨域资源共享（CORS）



### Websocket

> FastApi 有自带的websocket