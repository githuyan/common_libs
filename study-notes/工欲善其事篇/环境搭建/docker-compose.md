### Docker及docker-compose安装，配置

#### docker

参考：

1. [利用国内网络丝滑的安装 Docker](https://zhuanlan.zhihu.com/p/588264423) 

ubuntu20.04

```shell
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-add-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"

sudo apt update
sudo apt install -y docker-ce

sudo service docker start

# 添加组配置
sudo groupadd docker
newgrp docker
```

配置docker国内镜像源加速

**参考：**

- [Docker Hub 镜像加速器](https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6) 

在 `/etc/docker/daemon.json` 加入如下配置。

```json
{
        "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}
```





#### docker-compose

参考：

- [安装Docker Compose](https://dockerdocs.cn/compose/install/) 

- docker-compose地址：https://github.com/docker/compose/releases/

##### 自动安装：

> docker被封，导致只能使用官方安装包，下载较慢

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 添加权限
sudo chmod +x /usr/local/bin/docker-compose
```

##### 手动安装

1. 下载docker-compose二进制文件

   docker-compose: [docker-compose-linux-x86_64](D:\source\docker-compose) 

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

