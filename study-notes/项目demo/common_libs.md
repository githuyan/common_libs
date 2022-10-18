### 通用底层库（非服务）



#### rpc模块

**三方库调研：**

1. [aiohttp，httpx，requests 模块差异](https://zhuanlan.zhihu.com/p/103711201) 

[httpx](https://www.python-httpx.org/quickstart/) 的同步性能约等于90%的requests，异步性能约等于90%aiohttp，优势在于既能异步又能同步，语法简单，支持https。httpx活跃度优于aiohttp

**场景分析：**

> 同时并发大量请求的场景似乎很少

1. 正常微服务请求调用，可同步可异步。

