# python 协程和异步模块 `async`

> 协程通过 `async/await `语法进行声明，是编写异步应用的推荐方式
>
> 协程本质上就是一个线程，不过它是协作式的非抢占式的程序，面向的是IO操作。python有GIL的限制，不能充分利用多线程的实现高并发。进程和线程都是通过 `cpu` 的调度实现不同任务的有序执行，协程则要在写代码的时候确定执行顺序。由于协程在一个线程中，所以协程不能阻塞。
>
> 关于`asyncio`,python对这个异步应用的重构很是频繁，甚至python的一个小版本都可能更新一个使用方法，这段的学习只是基础的使用，主要是明白python异步模块的大致形式，另外本篇文章所用python版本为`Python 3.7.3 `

## 协程的创建

> 使用`asyncio`创建的协程底层依赖可等待对象包括协程, 任务 和 Future.

1. Future 对象

   - *Future* 对象用来链接 底层回调式代码 和高层异步/等待式代码。通常不会直接对Future对象操作

2. Task 对象

   - 如果一个对象可以在 [`await`](https://docs.python.org/zh-cn/3/reference/expressions.html#await) 语句中使用，那么它就是 **可等待** 对象
   
   - 创建任务
   
     ```python
     asyncio.create_task(coro, *, name=None)
     ```

```python
# 协程不能直接执行，需要在`asyncio.run()`中执行，也可以跟在`await`后面，直接调用会返回一个协程（coroutine）对象
import asyncio

async def test():
    print('异步函数开始测试！')
    await asyncio.sleep(2)
    print(f'异步函数测试结束')
    return

async def run():
    await test()
    
    # 将 test 协程 封装为一个 Task 并调度其执行。返回 Task 对象
    task = asyncio.create_task(test(), name=None)
    # 执行 Task 任务，当有返回值时，返回结果
    await task
    print(f'所有程序结束')

if __name__ == "__main__":
    asyncio.run(run(),debug=False)
```

2. 并发运行任务

   ```python
   import asyncio
   import time
   
   async def test(id):
       print(f'异步函数{id}开始测试！')
       await asyncio.sleep(2)
       print(f'异步函数{id}测试结束')
       return
   
   async def run():
       await asyncio.gather(test(1),test(2))
       
       #当一个协程通过 asyncio.create_task() 等函数被打包为一个 任务，该协程将自动排入日程准备立即运行:此时使用 await test 则会立即执行这个任务，并等待结束
       test = asyncio.create_task(test(3))
       await test
       print(f'所有程序结束')
   
   if __name__ == "__main__":
       start = time.time()
       asyncio.run(run())
       print(f'耗时{time.time()-start}')
       
   # 异步函数1开始测试！
   # 异步函数2开始测试！
   # 异步函数1测试结束
   # 异步函数2测试结束
   # 所有程序结束
   # 耗时2.0033013820648193
   ```

3. 与线程结合

   pass

## 参考

- [python协程 - 忞翛 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lczmx/p/14364580.html)
- [协程与任务 — Python 3.9.5 文档](https://docs.python.org/zh-cn/3/library/asyncio-task.html)

---

异步IO部分不太理解，导致无法总结成自己的东西，以后再总结

先不写时间

:dancer: ...
