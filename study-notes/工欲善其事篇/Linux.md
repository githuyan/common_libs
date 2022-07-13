### 技巧

#### 批量杀死指定进程

```
ps -ef | grep spiderks.py| awk '{print $2}' | xargs kill -9 
```
