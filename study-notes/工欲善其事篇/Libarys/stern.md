### stern工具

> Stern是一个非常有用的命令行工具,用于在Kubernetes集群中监控和查看多个pod和容器的日志输出。

**参考：**

- [如何使用 Stern 实时监控 Kubernetes Pod 日志 (linux-console.net)](https://cn.linux-console.net/?p=7881) 

官方地址：[Tags · stern/stern (github.com)](https://github.com/stern/stern/tags) 



#### stern安装

1. 下载stern

   ```shell
   # 下载stern压缩包，解压到 /usr/local/bin 目录，增加可执行权限
   
   wget https://github.com/stern/stern/releases/download/v1.20.0/stern_1.20.0_linux_amd64.tar.gz
   
   tar -xzvf stern_1.20.0_linux_amd64.tar.gz stern
   
   sudo cp ./stern /usr/local/bin
   sudo chmod +x stern
   
   # 检查stern版本
   stern --version
   ```

2. 指定配置

   ```shell
   # 下载指定集群配置文件 kubeconfig.yaml 
    
   # 替换stern默认配置路径
   ~/.kube/config
   
   # 将 kubeconfig.yaml 更名为 config
   cp kubeconfig.yaml config
   
   ```

   

#### 基础用法

##### 查看指定pod的日志

```shell
stern test-clm-client-api

stern test-clm-client-api*
```

- **`--since`** – 获取在人类可读的相对时间范围内写入的日志，自 `5m` 或 `1h`.

  ```shell
  --since 1h
  ```

- **`--tail`** – 获取许多这样的日志行作为开始。默认为 `-1`（无限制），因此在实时尾部输出开始之前，您的终端将充满所有预先存在的日志。

  ```shell
  --tail 100
  ```

- **`--exclude`** – 排除与此正则表达式匹配的日志行。您可以多次使用此标志；条件将组合为逻辑“和”子句。

  ```shell
  --exclude .*GET /robots.txt.*
  ```

- `--template` 指定输出模板，

  ```shell
  --template 'Namespace: {{.Namespace}} Pod: {{.PodName}} - {{.Message}}'
  ```

```shell
# 下面是一个从 Web 服务中获取有限选择的最近有意义的日志的示例：
stern .*web-server --since 1h --tail 100 --exclude .*GET /robots.txt.* --template 'Namespace: {{.Namespace}} Pod: {{.PodName}} - {{.Message}}'
```

