# FastApi

## 参数

查询参数



路径参数

请求体参数

## 依赖项

> 类似于 装饰器，一个功能函数总是位于末端的装饰器

**Depends**

```python
from typing import Optional

from fastapi import Cookie, Depends, FastAPI

app = FastAPI()

def query_extractor(q: Optional[str] = None):

    return q

def query_or_cookie_extractor(
    q: str = Depends(query_extractor), last_query: Optional[str] = Cookie(None)
):
    if not q:
        return last_query
    return q

@app.get("/items/")
async def read_query(query_or_default: str = Depends(query_or_cookie_extractor)):
    return {"q_or_cookie": query_or_default}
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





## 参考

- [子依赖项 - FastAPI (tiangolo.com)- 官方文档 --极好](https://fastapi.tiangolo.com/zh/tutorial/dependencies/sub-dependencies/) 

- [ 超全面整理fastAPI(从入门到运用)_fastapi](https://blog.csdn.net/my_name_is_learn/article/details/109819127)

