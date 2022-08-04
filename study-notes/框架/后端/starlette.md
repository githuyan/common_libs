# Starlette

> 一个标准的ASGI应用程序

**参考：** 

- [starlette源码分析 - 掘金 (juejin.cn)](https://juejin.cn/post/7035645583247278116)  -- 极好

**uviron介绍**

> `Uvicron`通过一个通用的协定接口与ASGI应用程序交互， 应用程序只要实现如下代码， 即可通过`Uvicorn`发送和接收信息

```python
async def app(scope, receive, send):
    # 一个最简单的ASGI应用程序
    assert scope['type'] == 'http'
    await send({
        'type': 'http.response.start',
        'status': 200,
        'headers': [
            [b'content-type', b'text/plain'],
        ]
    })
    await send({
        'type': 'http.response.body',
        'body': b'Hello, world!',
    })

if __name__ == "__main__":
    # uvicorn服务
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=5000, log_level="info")
```



## 中间件



