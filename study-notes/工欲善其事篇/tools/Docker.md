### Docker 容器

**参考：**

1. [docker-从入门到实战](https://yeasy.gitbook.io/docker_practice/image/dockerfile/arg#:~:text=Dockerfile%E4%B8%AD%E7%9A%84%20ARG%E6%8C%87%E4%BB%A4%E6%98%AF%E5%AE%9A%E4%B9%89%E5%8F%82%E6%95%B0%E5%90%8D%E7%A7%B0%EF%BC%8C%E4%BB%A5%E5%8F%8A%E5%AE%9A%E4%B9%89%E5%85%B6%E9%BB%98%E8%AE%A4%E5%80%BC%E3%80%82%20%E8%AF%A5%E9%BB%98%E8%AE%A4%E5%80%BC%E5%8F%AF%E4%BB%A5%E5%9C%A8%E6%9E%84%E5%BB%BA%E5%91%BD%E4%BB%A4%20docker%20build%E4%B8%AD%E7%94%A8,--build-arg%20%3C%E5%8F%82%E6%95%B0%E5%90%8D%3E%3D%3C%E5%80%BC%3E%E6%9D%A5%E8%A6%86%E7%9B%96%E3%80%82%20%E7%81%B5%E6%B4%BB%E7%9A%84%E4%BD%BF%E7%94%A8%20ARG%E6%8C%87%E4%BB%A4%EF%BC%8C%E8%83%BD%E5%A4%9F%E5%9C%A8%E4%B8%8D%E4%BF%AE%E6%94%B9%20Dockerfile%20%E7%9A%84%E6%83%85%E5%86%B5%E4%B8%8B%EF%BC%8C%E6%9E%84%E5%BB%BA%E5%87%BA%E4%B8%8D%E5%90%8C%E7%9A%84%E9%95%9C%E5%83%8F%E3%80%82)



#### 启动docker服务

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



#### **镜像**

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

#### **容器相关**

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

   查询日志

   - `docker logs 容器名/id -tf --tail 10`查看容器日志（加时间戳，允许日志输出后10条日志）

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

#### **容器数据卷 ** - **容器的持久化和同步操作**

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

#### DockerFile

> 用来构建docker镜像的文件！ 命令参数脚本 这个文件没有后缀名。创建一个自己的镜像，原来的nginx基础镜像，只能创建一个nginx的容器，而自定义的镜像可以是一个包含nginx+flask+python+uwsgi的一套环境

##### **基础指令**

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

1. 根据docker文件创建镜像

   docker build DockerFile[^默认寻找DockerFile文件进行构建]

   - -f CustomFile[^自定义文件名]

2. 实例

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

3. 提交一个镜像

   docker commit -a="author" -m="fiest commit"[^描述性内容] 容器id customimage[^自定义镜像名]:v1.0[^自定义版本号]

##### 实例

**ENV**: 

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



#### 整体流程

1. 全部指令

   ![](https://pic2.zhimg.com/v2-820aee2a33654099d87cdd2b7a1ce741_r.jpg?source=1940ef5c)



---

docker命令有跟linux命令和shell脚本相结合的部分，docker的常用功能依然需要经常使用才能熟练

:dancer: 现在是2021.6.20 ...