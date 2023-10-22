#### request, aiohttp, httpx 调研



**参考：**

- [浅度测评：requests、aiohttp、httpx 我应该用哪一个？](https://zhuanlan.zhihu.com/p/103711201) 



| 框架     | 同步，异步支持 | 并发能力 | 内存占用 | 代理支持        |
| -------- | -------------- | -------- | -------- | --------------- |
| requests | 同步           |          | 低       |                 |
| aiohttp  | 异步           |          | 低       | 不支持https代理 |
| httpx    | 同步，异步     |          | 中       |                 |

##### 流式支持



##### aiohttp与httpx的主要不同点

```python
# httpx
import httpx
import asyncio


async def main():
    async with httpx.AsyncClient() as client:
        resp = await client.post(
            'http://122.51.39.219:8000/query',
            json={'ts': '2020-01-20 13:14:15'}
        )  # 此时发送请求
        result = resp.json()
        print(result)


asyncio.run(main())

# aiohttp
import aiohttp
import asyncio


async def main():
    async with aiohttp.ClientSession() as client:
        resp = await client.post(
            'http://122.51.39.219:8000/query',
            json={'ts': '2020-01-20 13:14:15'}
        )
        result = await resp.json() # 此时发送请求
        print(result)


asyncio.run(main())
```

