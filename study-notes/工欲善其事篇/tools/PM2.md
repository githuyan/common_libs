## PM2

> 一个有node编写的进程管理工具，用于部署管理脚本运行，自动重启，日志采集等

### 基础命令

```shell
pm2 ls  // 查看当前管理的脚本
pm2 logs  // 查看实时日志情况
	logs <appname>
pm2 describe <appname> // 查看当前脚本详细信息

pm2 start hello.py 
pm2 stop hello.py 
	stop hello
pm2 delete hello

// 配置文件信息
pm2 init // 生成配置文件
pm2 start ecosystem.config.js  // 开始使用配置文件
// 文件中的内容也可以通过命令指定
pm2 start hello.py --interpreter=python2   // 指定脚本解释器为 python3
```

