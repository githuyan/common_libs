## PM2

> 一个有node编写的进程管理工具，用于部署管理脚本运行，自动重启，日志采集等

### 基础命令

**参考：** https://blog.csdn.net/qq_32281471/article/details/91369344

```shell
# 查看信息
pm2 ls  // 查看当前管理的脚本
pm2 logs  // 查看实时日志情况
	logs <appname>
pm2 describe <appname> // 查看当前脚本详细信息
pm2 show [name]

# 启动服务
pm2 start hello.py 
pm2 restart [name]
pm2 start ecosystem.config.js  # 开始使用配置文件

pm2 startup # 产生 init 脚本 保持进程活着，startup 是指系统boot, 开机进程自启动
pm2 unstartup # 禁用开机进程自启动

# 关闭服务
pm2 stop hello.py 
	stop hello
	pm2 stop all
pm2 delete hello

# 配置文件信息
pm2 init // 生成配置文件
pm2 start hello.py --interpreter=python2   // 文件中的内容也可以通过命令指定 指定脚本解释器为 python3
```

