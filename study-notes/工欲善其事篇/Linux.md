### 技巧

#### 批量杀死指定进程

> 只要把主进程杀死就可以杀死所有进程（孤儿进程？？）

```
ps -aux | grep python | awk '{print $2}' | xargs kill -9
```
