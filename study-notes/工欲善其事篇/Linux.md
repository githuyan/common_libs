#### 批量杀死所有进程

ps -aux | grep python | awk '{print $2}' | xargs kill -9



#### 基础命令

```shell
mkdir 
	-p /usr/home/test/aaa  # 根据路径创建目录，路径存在时自动创建
```



