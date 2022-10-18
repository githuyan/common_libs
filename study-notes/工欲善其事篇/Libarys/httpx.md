### httpx

**参考：**

1. https://www.python-httpx.org/quickstart/



post 之类的用些大量数据的请求方式，使用 data 存放简单数据（表单数据），使用 json 存放复杂数据，使用 content 存放二进制数据



##### 流式响应

> 对于大量数据的相应，并不一次性直接接收到内存中
>
> 这里可以做限流处理

```python
# 注意流式响应，不能直接获取响应属性，需要先 read，再获取
>>> with httpx.stream("GET", "https://www.example.com") as r:
...     if r.headers['Content-Length'] < TOO_LONG:
...         r.read()
...         print(r.text)
```



##### 默认超时

httpx对于所有网络请求都设置了默认5s的超时时间，

```python
r = httpx.get("http://www.baidu.com", timeout=None)  # 关闭默认超时时间
```



##### 认证author	

```python
>>> httpx.get("https://example.com", auth=("my_user", "password123"))
# 或者
>>> auth = httpx.DigestAuth("my_user", "password123")
>>> httpx.get("https://example.com", auth=auth)
<Response [200 OK]>
```



##### 代理路由

> 通配符匹配，流量控制

```python
# 代理域“example.com”上的所有请求，让其他请求通过.
proxies = {
    "all://": "http://localhost:8030",
}
```





