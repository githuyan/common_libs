### JWT

**参考：** [(145条消息) jwt的详细说明_GoverChan的博客-CSDN博客_jwt的sub](https://blog.csdn.net/qq_15766181/article/details/80707923)

1. 头部

   ```python
   # 头部承载声明类型，声明加密算法。
   # 整体使用base64加密
   {
     'typ': 'JWT',
     'alg': 'HS256'
   }
   ```

2. 载荷

   ```python
   # 存放一些不重要的信息
   # 整体使用base64加密
   {
     "sub": "1234567890",
     "name": "John Doe",
     "admin": true
   }
   ```

3. 签名

   ```python
   # 由 base64加密后的头部 + base64加密后的载荷，之后使用头部声明的加密算法辅以服务端的私钥加密而成
   eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
   ```

**优点：**

1. 由于加密内容是即时算出来的，不想session机制在服务端存储用户信息，所以对服务器的压力更小，且对于分布式服务更加友好
2. 因为json的通用性，所以JWT是可以进行跨语言支持的，像JAVA,JavaScript,NodeJS,PHP等很多语言都可以使用。
3. 因为有了payload部分，所以JWT可以在自身存储一些其他业务逻辑所必要的非敏感信息。
4. 便于传输，jwt的构成非常简单，字节占用很小，所以它是非常便于传输的。
5. 它不需要在服务端保存会话信息, 所以它易于应用的扩展



#### jwt示例

```python
async def generate_jwt_token(payload: Dict) -> str:
    """根据用户id生成token"""
    # _payload = {**payload, 'exp': int(time.time())} ## exp字段会被自动解析为过期时间，当exp>now则token已过期
    token = jwt.encode(payload, settings.jwt_secret, algorithm=settings.jwt_algorithm)
    return token

async def verify_jwt_token(token: str) ->  Dict:
    """验证用户token"""
    if not token:
        return {}
    try:
        payload = jwt.decode(token, settings.jwt_secret, algorithms=[settings.jwt_algorithm])
    except jwt.PyJWTError:
        logger.warning("jwt token校验失败")
        return {}

    # token不过期
    return payload

```

