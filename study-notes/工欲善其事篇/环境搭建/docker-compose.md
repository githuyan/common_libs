### Docker及docker-compose安装，配置

#### docker





#### docker-compose

docker-compose地址：https://github.com/docker/compose/releases/

##### 自动安装：

> docker被封，导致只能使用官方安装包，下载较慢

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 添加权限
sudo chmod +x /usr/local/bin/docker-compose
```

##### 手动安装

1. 下载docker-compose二进制文件

   docker-compose: [docker-compose-linux-x86_64](D:\source\docker-compose-linux-x86_64) 

   ```shell
   # 系统：uname -s, 架构：uname -m 
   ```

2. 上传到`/usr/local/bin/`命名为`docker-compose`

3. 配置执行权限

   ```shell
   sudo chmod +x /usr/local/bin/docker-compose
   ```

4. 安装验证

   ```shell
   docker-compose --version
   ```

