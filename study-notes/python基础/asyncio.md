# 异步

## async.timeout

> 兼容async的超时的上下文管理器

```python
# 如果1.5s可以运行完打印true,否则打印false,表示超时。
async def inner():
    import time
    time.sleep(2)

async def aa():
    async with timeout(1.5) as cm:
        await inner()
    print(cm.expired)
    # loop = asyncio.get_event_loop()
    # await asyncio.wait_for(inner(), 1, loop=loop)

asyncio.run(aa())

# 或者 
asyncio.wait_for(inner, timeout, *, loop=None)
```



