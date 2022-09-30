

## Docker-compose

> Docker Compose 是一个在单个服务器或主机上创建多个容器的工具

### 命令

```python
# 启动，重启
docker-compose up  # 用于部署一个compose应用，默认使用 docker-compose.yaml 文件
docker-compose up -f docker-compose-yyy.yaml  # 自定义 docker-compose 文件名
docker-compose restart  # 重启关闭的应用，如果在重启前，修改了应用内容，并不会对重启的应用生效，除非重新部署

# 停止，删除
docker-compose stop  # 停止Compose应用相关的所有容器，但不会删除他们
docker-compose rm  # 删除已停止的compose应用，他会删除容器和网络，但不会删除卷和镜像
docker-compose down  # 停止并删除运行中的compose应用，他会删除容器和网络，但不会删除卷和镜像

# 查询	
docker-compose ps  # 用于列出compose应用的各个容器，输出状态，容器运行的命令以及映射端口
```





## Docker-swarm

> Docker Swarm 则可以在多个服务器或主机上创建容器集群服务



## Docker 

> Dockerbr本身就相当于一个虚拟机，他本身就是一个Docker环境，理论上可以在任何下载Docker的设备商运行程序
>
> **docker 的镜像类似于python的类， 容器相当于python的实例， docker本身类似于python进程的docker进程**

**参考：**

1. [docker-从入门到实战](https://yeasy.gitbook.io/docker_practice/image/dockerfile/arg#:~:text=Dockerfile%E4%B8%AD%E7%9A%84%20ARG%E6%8C%87%E4%BB%A4%E6%98%AF%E5%AE%9A%E4%B9%89%E5%8F%82%E6%95%B0%E5%90%8D%E7%A7%B0%EF%BC%8C%E4%BB%A5%E5%8F%8A%E5%AE%9A%E4%B9%89%E5%85%B6%E9%BB%98%E8%AE%A4%E5%80%BC%E3%80%82%20%E8%AF%A5%E9%BB%98%E8%AE%A4%E5%80%BC%E5%8F%AF%E4%BB%A5%E5%9C%A8%E6%9E%84%E5%BB%BA%E5%91%BD%E4%BB%A4%20docker%20build%E4%B8%AD%E7%94%A8,--build-arg%20%3C%E5%8F%82%E6%95%B0%E5%90%8D%3E%3D%3C%E5%80%BC%3E%E6%9D%A5%E8%A6%86%E7%9B%96%E3%80%82%20%E7%81%B5%E6%B4%BB%E7%9A%84%E4%BD%BF%E7%94%A8%20ARG%E6%8C%87%E4%BB%A4%EF%BC%8C%E8%83%BD%E5%A4%9F%E5%9C%A8%E4%B8%8D%E4%BF%AE%E6%94%B9%20Dockerfile%20%E7%9A%84%E6%83%85%E5%86%B5%E4%B8%8B%EF%BC%8C%E6%9E%84%E5%BB%BA%E5%87%BA%E4%B8%8D%E5%90%8C%E7%9A%84%E9%95%9C%E5%83%8F%E3%80%82)



### 启动docker服务

```shell
# 启动
# 在真实 ubuntu 系统中
systemctl start docker
systemctl deamon-reload # 后台启动
systemctl restart docker # 重启

# 在虚拟（wsl2 window linux 子系统） 中
sudo service docker start

#关闭
docker service docker stop
docker systemctl stop docker
```



### **镜像**

> 镜像是一种轻量级，可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码，运行时的库，运行环境和配置文件

1. **镜像查询**

   `docker images`

   - `-a` 列出所有镜像
   - `-q` 只显示镜像id

   | REPOSITORY | TAG  | IMAGE  | CREATED | SIZE |
   | ---------- | ---- | ------ | ------- | ---- |
   | 镜像       | 版本 | 镜像id | 时间    | 大小 |

2. **镜像搜索**

   `docker search mysql` 搜索mysql镜像

   - --filter=STARTS=3000 搜索所有starts>=3000的镜像

3. **镜像上传**

   **参考：** https://cloud.tencent.com/developer/article/1549825

1. **镜像下载**

   `docker pull mysql` 拉取下载镜像，默认下载最新版镜像

   - mysql:5.7 指定下载版本

2. **镜像删除**

   `docker rmi -f id` 根据镜像id删除镜像

   - -f $(docker images -a -q) 删除所有镜像

### **容器相关**

1. **容器的创建并启动**

   > 开始后 docker 会在本机寻找镜像，判断本机是否有这个镜像（image),如果有就运行这个镜像
   >
   > 如果没有就去 Docker Hub上下载，DockerHub上如果有这个镜像，就下载这个镜像，然后运行这个镜像，没找到这个镜像就会报错

   `docker run [可选参数] image` 

   - `-it` 使用交互方式运行，创建容器并进入容器
   - `-d` 使用后台运行方式
   - `-p` 查看容器端口，端口映射 -p 8080[^主机端口] :8080[^容器端口]，可以同时暴露多个端口 -p 8080:80 -p3306:3306
   - docker run -it centos /bin/bash 这指定了景区容器后的控制台
   - `-P` 随机指定端口映射
   - `-v` 数据卷挂载
   - `-e` 环境配置

   进入当前正在运行的容器

   - `docker exec -it 容器名/id /bin/bash`  重新进入这个运行的容器时，需要指定控制台（通常使用）
   - `docker attach 容器名/id` 进入容器，正在执行的终端，不会启动新的进程

2. **容器的启动与停止**

   docker start 容器id

   - restart 容器id
   - stop 容器id
   - kill 容器id  强制停止

3. **容器查询**

   `docker ps ` 当前正在运行的容器

   - `-a` 列出当前和历史运行过的容器
   - `-q` 只显示容器的id
   - `-n=1` 只显示最近运行过的1个容器

   **查询日志**

   **参考：**

   1. https://blog.csdn.net/m0_59092234/article/details/124169005 

   ```shell
   # docker 日志存储位置
   /var/lib/docker/containers/ID/ID-json.log
   
   # docker logs [OPTIONS] CONTAINER
     Options:
           --details        显示更多的信息
       -f, --follow         跟踪实时日志
           --since string   显示自某个timestamp之后的日志，或相对时间，如42m（即42分钟）
           --tail string    从日志末尾显示多少行日志， 默认是all
       -t, --timestamps     显示时间戳
           --until string   显示自某个timestamp之前的日志，或相对时间，如42m（即42分钟）
           
   docker logs --since 30m CONTAINER_ID  # 实时查看最近30分钟日志
   docker logs -t --since="2018-02-08T13:23:37" CONTAINER_ID # 实时查看某个时间点之后的日志
   
   `docker logs 容器名/id -tf --tail 10`查看容器日志（加时间戳，允许日志输出后10条日志）
   ```

   

   查看容器中的进程信息

   - `docker top 容器名/id` （pid是进程id，ppid是父进程id）

   查询容器元数据信息

   - `docker inspect 容器名/id`   inspect 指令同样可以查询镜像的详细信息

   查看CPU内存状况

   - ` docker stats` 默认查看所有容器的CPU内存占用情况

     容器id  追加id可以查看指定容器的CPU使用情况

4. **容器退出与删除**

   `exit` 容器停止并退出

   `Ctrl + P + Q` 容器不停止但退出

   `docker rm 容器名/id` 正在运行的容器不能删除

   `docker rm -f $(docker ps -aq)` 强制删除所有容器

### **容器数据卷 ** - **容器的持久化和同步操作**

> 数据如果都存在容器中，容器删除后，数据会丢失，卷技术就是容器之间的数据共享技术，将容器中产生的数据同步到本地

1. **数据卷的挂载**

   > bind mount(挂载) a volume 这是一个双向绑定，两个地址指向同一片内存，无论在哪个目录操作都会同时作用到双方的目录，但是以主机中的目录为主，无法删除容器中的挂载目录，但是可以删除主机中的嘎在目录，

   - **挂载**

     docker -it -v /home/test[^本机目录absolute] : /home[^容器目录] --name[^容器名称] image[^镜像名称] /bin/bash[^目录映射]

     - 可以创建多个映射，只需 -v 映射 -v 映射

   - **设置权限**

     docker run -d -P -v 卷名 : 容器内目录路径 : 权限 镜像名 

     - ro 只读，指定容器内的卷对外部具有只读权限，则数据由主机写入
     - rw 读写  指定容器的卷能写入主机

   - **挂载方式**

     - -v 容器内目录  									匿名挂载
       - sudo docker run -d --name 容器名 -P -v 卷名 : 目录名 镜像名
     - -v 卷名：容器内路径 		 				具名挂载
       - sudo docker run -d --name nginxa -P -v juming-nginx:/etc/nginx nginx 这是具名挂载
     - -v 主机目录路径：容器内目录路径    指定路径挂载
       - sudo docker run -d --name nginxb -P -v /home/githuyan/桌面:/etc/nginx nginx 这是目录映射挂载

2. **数据卷的查询**

   `docker volume ls` 查看所有卷的情况

   - inspect 卷名 (具名挂载的名字) 检查指定卷的详细情况
   - prune  移除所有不用的卷名
   - rm 卷名 
   - create 卷名

3. **数据卷继承**

   > 容器间的挂载就是继承，一个父容器可以有多个子容器，而他们的文件共享相当于地址指针都指向了主机所挂载的目录，任意删除某一个子容器的挂载卷，不影响其他子容器的卷

   sudo docker -d --name nginxa -v /home/githuyan/桌面/nginxa:/home/test nginx 这是父容器

   sudo docker -d --name nginxb --volumes-from nginxa[^父容器] nginx[^镜像名]  这是子容器

   由于父容器创建时 test 目录挂载了 nginxa 目录，所以以后继承父容器的子容器也将挂载同一个目录，主机/nginxa目录，父容器/test目录，子容器/test目录，这三个目录信息同步

### DockerFile

> 用来构建docker镜像的文件！ 命令参数脚本 这个文件没有后缀名。创建一个自己的镜像，原来的nginx基础镜像，只能创建一个nginx的容器，而自定义的镜像可以是一个包含nginx+flask+python+uwsgi的一套环境

#### **基础指令**

> 就是一个脚本

| 指令               | 含义                                                         | 备注                                |
| ------------------ | ------------------------------------------------------------ | ----------------------------------- |
| FROM               | 基础镜像                                                     |                                     |
| MAINTAINER         | 这个镜象的作者 姓名+邮箱                                     | yang<123456@qq.com>                 |
| ENV                | 构建的时候设置环境变量（ -e） 这是键值对的形式               | MYPATH /home/dockerfile             |
| RUN                | 镜像构建时需要运行的命令                                     |                                     |
| ADD filename /path | 添加依赖文件到指定路径                                       | 相当于拷贝ADD jdk.tar.gz /usr/local |
| WORKDIR            | 镜像的工作目录                                               |                                     |
| VOLUME             | 挂载的目录                                                   | COLUME ["colume1","colume2"]        |
| EXPOSE             | 暴露端口。如果不指定，就需要在创建容器时，使用-p暴露端口     |                                     |
| CMD                | 指定这个容器启动时要运行的命令（脚本）只有最后一个会生效，而且可被替代 |                                     |
| ENTRYPOINT         | 指定这个容器时要运行的的命令，可以追加命令                   |                                     |
| ONBUILD            | 当构建一个继承自这个文件的镜像时，会触发这个指令             |                                     |
| COPY               | 类似ADD命令，将文件拷贝到镜像中                              |                                     |

#### 构建镜像

> docker构建镜像实际上是将本地的文件发送到远程docker服务器上，使用docker引擎构建，这是一组docker api
>
> 远程和本地的路径差异就出现了 上下文， 所以构建镜像最好使用 **绝对路径， 并空值dockerfiel的目录**

```shell
# docker build [选项] <上下文路径/URL/->

$ docker build -t nginx:v3 .
Sending build context to Docker daemon 2.048 kB  # 将当前本地要构建镜像的上下文 发送到远程服务器
Step 1 : FROM nginx								# 拉取基础镜像，
 ---> e43d811ce2f4
Step 2 : RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html # 在基础镜像中运行命令，得到第二层镜像
 ---> Running in 9cdc27646c7b
 ---> 44aa4490ce2c
Removing intermediate container 9cdc27646c7b  # 删除第一层镜像
Successfully built 44aa4490ce2c
```

**其他构建方式**



docker build DockerFile[^默认寻找DockerFile文件进行构建]

- -f CustomFile[^自定义文件名]

1. 实例

   > 这整个文件写下来，每一句命令都是单独的一层，所以过多无意义的层，会造成镜像膨胀过大，首先from 继承centos环境，然后作者，配置工作目录，添加（相当于拷贝）应用压缩包到指定目录并解压缩，暴露端口 cmd执行命令

   ```dockerfile
   #菜鸟教程例子
   FROM centos
   RUN **yum install** **wget**
   RUN **wget** -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"
   RUN **tar** -xvf redis.tar.gz
   
   # 以上执行会创建 3 层镜像。可简化为以下格式：
   FROM centos
   RUN yum install wget \
         && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
         && tar -xvf redis.tar.gz
   # 以 && 符号连接命令，这样执行后，只会创建 1 层镜像。
   ```

   sudo docker build -f /home/dockerfile -t aaanginx:1.0 .

   根据 home目录下的dockerfile文件创建了一个名叫 aaanginx 版本号为1.0的镜像在 当前目录下 ( . )

2. 提交一个镜像

   docker commit -a="author" -m="fiest commit"[^描述性内容] 容器id customimage[^自定义镜像名]:v1.0[^自定义版本号]

#### 实例

> 想输入外部参数或许可以利用 RUN 命令运行shell脚本的方式添加

##### **FROM**

> 指定基础镜像
>
> docker 项目的迭代，docker第二层镜像 = 第一层镜像 + 存储层（修改）数据，docker commit 之后，此时的docker镜像属于第二层
>
> **挂在在卷中的数据不会记录进容器存储层**

```dockerfile
# FROM 指令并不是必须的，甚至可以指定一个空白镜像, 
FROM scratch
...
```

##### **RUN**

> run命令实在运行 shell 命令， 每一个 RUN 都是在构建一层镜像，过多的 RUN 会导致镜像冗余，应该使用尽量少的 RUN 
>
> 镜像构建时，一定要确保每一层只添加真正需要添加的东西，**任何无关的东西都应该清理掉**。

```dockerfile
FROM debian:stretch

# 这里构建了7层镜像
RUN apt-get update
RUN apt-get install -y gcc libc6-dev make wget
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"
RUN mkdir -p /usr/src/redis
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install

# 正确写法
RUN set -x; buildDeps='gcc libc6-dev make wget' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \    # 这一组命令的最后添加了清理工作的命令， 
    && rm redis.tar.gz \     # 删除了为了编译构建所需要的软件
    && rm -r /usr/src/redis \   # 清理了所有下载、展开的文件
    && apt-get purge -y --auto-remove $buildDeps  # 清理了 apt 缓存文件
    
    #镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。
```

1. **CMD**

   > 和 RUN 类似，docker没有前台后台的概念，一个docker 进程都应该是前台执行，**直接执行可执行文件**

   ```dockerfile
   # ubuntu 中的 bash 命令默认为 /bin/bash
   CMD echo $HOME  === CMD [ "sh", "-c", "echo $HOME" ]   # 必须使用双引号
   # 相当于 sh -c "echo $HOME"
   
   FROM ubuntu:18.04
   RUN apt-get update \
       && apt-get install -y curl \
       && rm -rf /var/lib/apt/lists/*
   CMD [ "curl", "-s", "http://myip.ipip.net" ]
   ```

   

##### COPY

> linux  中的 cp，可以使用正则， 单正则要满足 go 语言的正则标准

```dockerfile
# 目标路径为空时会自动创建
COPY  <源路径>... <目标路径>

# 在使用该指令的时候还可以加上 --chown=<user>:<group> 选项来改变文件的所属用户及所属组
COPY --chown=55:mygroup files* /mydir/
```

1. **ADD**

   > 和 COPY 功能类似，只是他的功能定位不清晰，官方**仅建议使用他的自动解压缩功能**

   ```dockerfile
   # 另一个功能， 自动解压缩到另一个文件中，
   ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
   ```



**ENV**: 

> 设置环境变量, 环境变量可以使用的地方很多，很强大。通过环境变量，我们可以让一份 `Dockerfile` 制作更多的镜像，只需使用不同的环境变量即可

```dockerfile
# 可以设置多个变量
ENV VERSION=1.0 DEBUG=on \
    NAME="Happy Feet"
```

1. **ARG**

   > ARG 指令有生效范围，如果在 `FROM` 指令之前指定，那么只能用于 `FROM` 指令中。
   >
   > 使用上述 Dockerfile 会发现无法输出 `${DOCKER_USERNAME}` 变量的值，要想正常输出，你必须在 `FROM` 之后再次指定 `ARG`

   ```dockerfile
   # 相当于设置了一个 变量
   
   # 只在 FROM 中生效
   ARG DOCKER_USERNAME=library
   
   FROM ${DOCKER_USERNAME}/alpine
   
   # 要想在 FROM 之后使用，必须再次指定
   ARG DOCKER_USERNAME=library
   
   RUN set -x ; echo ${DOCKER_USERNAME}
   ```

​	

##### WORKDIR

> 在dockerfile 文件中指定当前的工作路径, **改变环境状态并影响以后的层**
>
> 若路径不存在，会自动创建
>
> 

```dockerfile
WORKDIR /app
RUN echo "hello" > world.txt
WORKDIR /app/api
COPY ./word.txt .
RUN word.txt
```



​		

#### 整体流程

1. 全部指令

   ![](https://pic2.zhimg.com/v2-820aee2a33654099d87cdd2b7a1ce741_r.jpg?source=1940ef5c)



---

docker命令有跟linux命令和shell脚本相结合的部分，docker的常用功能依然需要经常使用才能熟练

:dancer: 现在是2021.6.20 ...