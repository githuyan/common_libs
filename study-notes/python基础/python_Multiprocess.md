#### python并发之进程

> 由于Windows没有fork，创建子进程时，类似是直接 import 了子进程对象所在的整个模块，如果被调用模块，也创建了子进程，那么会出现递归，无限创建子进程，所以，主进程一定要写在 `if __name__ == '__mian__':`下面
>
> python进程的使用方法与python线程有很多相似之处，大部分方法都可以正常使用
>
> 如果是I/O密集型任务，考虑多线程，如果是计算密集型任务，考虑多进程

##### 启动和停止进程

> 进程和线程的操作方法差不多，但是开启过多的进程可能并不会提高效率，进程之间的调度很是消耗计算机资源

- 僵尸进程-[参考](https://www.cnblogs.com/Anker/p/3271773.html)，只有进程开始，没有将进程关闭
- 孤儿进程，进程嵌套进程，主进程意外关闭，而子进程不被关闭

```python
__init__(self, group=None, target=None, name=None, args=(), kwargs={},
             *, daemon=None)
```

- `group`：指定所创建的进程隶属于哪个进程组
- `target`：指定所创建的进程要调度的目标方法
- `name`: 指定进程名字
- `args`：以元组的方式，为 target 指定的方法传递参数,`args=(param,)`[^一定要这样]
- `kwargs`：以字典的方式，为 target 指定的方法传递参数
- `daemon`：指定所创建的进程是否为守护进程（后台进程，只要主进程执行完毕，不管子进程有没有结束，都会被强制退出）

```python
from multiprocessing import Process,current_process
import time

def test(*args):
    print(f'进程-{current_process().name}测试开始')
    time.sleep(2)
    print(f'进程-{current_process().name}测试结束')


if __name__ == "__main__":
    start = time.time()
    a = Process(target=test)
    a.start()

    b = Process(target=test)
    b.start()
    
    a.join()
    b.join()
    print(f'耗时{time.time()-start}')
    
# 进程-Process-1测试开始
# 进程-Process-2测试开始
# 进程-Process-1测试结束
# 进程-Process-2测试结束
# 耗时2.5092406272888184  # 会发现慢了0.5秒，这是因为进程之间的切换要比进程慢很多
```

- `.start()` 开启进程
- `.join()` 等待此进程运行结束，再运行主进程（普通情况下，主进程只要将子进程开启，就会开始运行接下来的部分，不管子进程有没有结束）
- `.is_alive()` 判断此进程是否还存在
- `.terminate()` 中断进程，不管此进程有没有结束，如果他还有子进程，则子进程不会结束，造成孤儿进程
- `.name` 获取此进程名字，`.name = new_process`，设置该进程名字[^这是使用了setter的效果]，若没有指定，则默认`Process-num`
- `.darmon`或 `.daemon=True` [^这是使用了setter的效果] 设置此进程为守护进程，注意，这个方法一定要用在`.start()`方法之前，活跃的进程无法再被设置为守护进程
- `.pid` 返回进程的ID

**自定义进程**

> 继承式自定义进程，只需要向自定义线程那样，重写run方法

```python
class MyProcess(Process):
    def __init__(self):
        super().__init__()
    def run(self):
        print(f'为{current_process().name}自定义提示信息')

if __name__ == "__main__":
    start = time.time()

    b = MyProcess()
    b.start()
    b.join()

    print(f'耗时{time.time()-start}')
```

- 僵尸进程

##### **进程池**

> 自定义开启多个进程的方法与自定义开启多线程的方法差不多，但是进程池有一些区别

- `apply(func[, args[, kwds]])`: 阻塞方式调用函数（放进进程池的任务按顺序执行），`args`表示以元组方式给函数传参，`kwds`表示以字典方式给函数传参
- `apply_async(self, func, args=(), kwds={}, callback=func)` ：使用非阻塞方式调用函数（放进进程池的任务并行执行），`args`表示以元组方式给函数传参，`kwds`表示以字典方式给函数传参
  - 这个回调函数，会自动在此进程结束后，将返回的结果传到回调函数`func`中
- `map(self, func, iterable)` 同时将多个任务添加进进程池
- `close()`：关闭Pool，使其不再接受新的任务
- `terminate()`：不管任务是否完成，立即终止
- `join()`：主进程阻塞，等待子进程的退出， 必须在close或terminate之后使用

```python
from multiprocessing import Process,current_process,Pool
import time

def callback_func(r):
    print(r)
    print(f'这是{current_process().name}的回调函数')

def test(*args):
    print(f'进程-{current_process().name}开始测试')
    time.sleep(2)
    print(f'进程-{current_process().name}测试结束')
    return 1

if __name__ == "__main__":
    start = time.time()

    pool = Pool(maxtasksperchild=3) # 默认同时开启的任务数，其他的会被阻塞，直到某一个进程完成任务，释放资源
    pool.apply_async(test,callback=callback_func)
    pool.apply_async(test,callback=callback_func)
    pool.apply_async(test,callback=callback_func)
    # 或者
    # pool.map(test, [1, 2, 3])

    pool.close() # 关闭进程池，不能再添加任务
    pool.join() # 使这些子进程与主进程同步

    print(f'耗时{time.time()-start}')
    
# 进程-SpawnPoolWorker-1开始测试
# 进程-SpawnPoolWorker-2开始测试
# 进程-SpawnPoolWorker-3开始测试
# 进程-SpawnPoolWorker-1测试结束
# 1
# 这是MainProcess的回调函数    # 会发现回调函数是在主进程中，
# 进程-SpawnPoolWorker-2测试结束
# 1
# 这是MainProcess的回调函数
# 进程-SpawnPoolWorker-3测试结束
# 1
# 这是MainProcess的回调函数
# 耗时3.18084716796875
```

2. 自定义启动多进程

   ```python
   #也可以直接用列表的方法启动多个进程，但结束需要一个一个的结束
   p_list= []
   for i in range(10):
       p = Process(target=func)
       p_list.append(p)
       p.start()   #这些开启的子进程都是异步的
   [ap.join() for ap in p_list] # 这条语句使最后一个子进程与主（父）进程同步
   ```

##### 进程间通信

1. **进程队列**

   > 生产者消费者模式，通python线程间以队列通信

2. **管道**

   > Pipe的实例是一个全双工的管道，一段发信息，一段收信息
   >
   > 缺点在于功能少，放进去的东西只能在另一端取出，管道是先进先出

   `l_conn,r_conn = Pipe(duplex=True)`,一个管道实例的两个端口，当`duplex=False`是一个半双工管道

   `.send(message)` 向管道中传送一个message

   `.resv()` 从管道中接收数据

   `.poll(timeout=None)` 返回一个bool 会在运行到这里的时候发起一次查询，如果为False，就在此阻塞，就在2秒后在查询一次如果管道已经被关闭，那么会抛出`EOFError`。

   ```python
   from multiprocessing import Pipe
   
   def test(l_conn,*args):
       print(f'进程-{current_process().name}开始测试')
       l_conn.send('这里是子进程')
       l_conn.send('这是第二条数据')
       time.sleep(2)
       print(f'进程-{current_process().name}测试结束')
       return 1
   
   if __name__ == "__main__":
       l_conn,r_conn = Pipe()
   
       p = Process(target=test,args=(l_conn,))
       print(r_conn.poll(timeout=1)) # 此时还没有进程向管道内发送数据
       time.sleep(3)
       p.start()
   
       p.join()
       print(r_conn.poll(timeout=1))  # 此时管道内有数据
       print(r_conn.recv())  # 收到管道的数据
   # False  
   # 进程 - Process - 1
   # 开始测试
   # 进程 - Process - 1
   # 测试结束
   # True
   # 这里是子进程 
   ```

3. 锁 - Lock

   > 与多线程相同的用法

   ```python
   from multiprocessing import Lock
   ```

   - `.acquire()` 获得锁

   - `.release()` 释放锁
   - `.locked()` 检查是否获得锁

4. 信号量 - Semaphore

   > 与多线程相容的用法，是多个资源能同时访问一个资源

   ```python
   from multiprocessing import BoundedSemaphore
   ```

5. 事件 - Event

   > 与多线程相同的用法

   ```python 
   from multiprocessing import Event
   ```

   - `.set()` 将flag设为True ，就像是获取锁
   - `.clear()` 将flag设为False，就像是释放锁
   - `.is_set()` 检查当前flag的bool
   - `wait(timeout=None)` 若 flag=True则继续运行，若为False则阻塞，知道flag=True

##### 多进程与多线程

> 根本区别：进程是操作系统资源分配的基本单位，而线程是任务调度和执行的基本单位

- 练习 多进程(3个)+多线程(10个)

  ```python
  from concurrent.futures import ThreadPoolExecutor,ALL_COMPLETED,wait
  from multiprocessing import Process,current_process,Pool
  import time
  
  def test(*args):
      print(f'进程-{current_process().name}--{current_thread().name}开始测试')
      time.sleep(2)
      print(f'进程-{current_process().name}--{current_thread().name}测试结束')
  
  def multi_threads():
      """创建多线程"""
      t_pool = ThreadPoolExecutor(max_workers=10)
      t_pool.map(test,[_ for _ in range(10)])
      wait(t_pool,return_when=ALL_COMPLETED)
      return f'{current_process().name}执行结束,\n'
  
  if __name__ == "__main__":
      start = time.time()
  
      p_pool = Pool(3)
      for i in range(3):
          p_pool.apply_async(multi_threads)
  
      p_pool.close()
      p_pool.join()
      print(f'耗时{time.time()-start}全部进程执行结束，全部线程执行结束')
      
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_0开始测试
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_1开始测试
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_2开始测试
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_3开始测试
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_4开始测试
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_5开始测试
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_6开始测试
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-0_7开始测试
  # ...
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-2_8测试结束进程-SpawnPoolWorker-1--ThreadPoolExecutor-2_9测试结束
  # 进程-SpawnPoolWorker-1--ThreadPoolExecutor-2_7测试结束
  # 
  # 耗时2.547154188156128全部进程执行结束，全部线程执行结束
  ```

##### 参考

- [Python多进程编程](https://www.cnblogs.com/kaituorensheng/p/4445418.html)
- [python进程、线程、协程 - 张岩林 - 博客园 (cnblogs.com)](https://www.cnblogs.com/aylin/p/5601969.html)
- [一篇文章搞定Python多进程(全) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/64702600)

---

> 上述总结是我的个人理解，若有不准确的地方，欢迎指正！	

​	多进程与多线程的大多数方法都很相似，便于记忆，多进程与多线程在底层其实与 Future 对象和 Test 对象有关，包括后面要整理的协程和异步IO，但是这部分的资料讲的好的不多，日后有时间再细细研究

:dancer: ...
