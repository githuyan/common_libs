### Linux理论知识

**参考：**

- [Linux 目录结构：Unix 系统资源目录（/usr](https://learnku.com/server/wikis/36496) 

| 目录      | 描述                                                         | 备注 |
| --------- | ------------------------------------------------------------ | ---- |
| **/**     | 根目录，只包含所有文件结构的最顶层的目录                     |      |
| **/bin**  | 存放可执行文件的地方。所有的用户都可以访问                   |      |
| **/dev**  | 设备驱动程序存放的目录                                       |      |
| **/etc**  | 系统配置文件目录，该目录包含配置文件，有效用户列表，用户的群组，以太网，主机等配置 |      |
| **/lib**  | 根目录下的所程序的共享库目录，也包含一些与内核相关的文件     |      |
| **/home** | 普通用户的家目录                                             |      |
| **/mnt**  | 用于挂载其他临时文件系统，例如分别用于 CD-ROM 驱动器和软盘驱动器的 CDROM 和软盘。 |      |
| **/tmp**  | 系统运行的物理内存和物理磁盘交互的缓存文件                   |      |
| **/usr**  | 操作系统软件资源所放置的目录，可以被多个用户使用。包括管理命令、共享文件、库文件以及其他 |      |
| **/var**  | 包含内容经常变化的文件，文件的大小可能会改变，如缓冲文件，日志文件，缓存文件，等一般都存放在这里 |      |



### 基础命令

```shell
mkdir 
	-p /usr/home/test/aaa  # 根据路径创建目录，路径存在时自动创建
	
useradd 
	-r # 创建系统用户
	-g # 将这个用户添加进群组
		test_group
groupadd
	-r # 创建系统组
```

##### 查看路径下所有文件

```shell
ls ./*
```



##### xargs  命令传递（管道）

> xargs 它能够捕获一个命令的输出，然后传递给另外一个命令

**参考：**

- https://www.runoob.com/linux/linux-comm-xargs.html 

```shell
cat url-list.txt | xargs wget -c
```



**grep模式匹配命令**

```shell
一、 基本操作
grep命令用于打印输出文本中匹配的模式串，它使用正则表达式作为模式匹配的条件。grep支持三种正则表达式引擎，分别用三个参数指定：

参数 说明

- -E POSIX扩展正则表达式，ERE
- -G POSIX基本正则表达式，BRE
- -P Perl正则表达式，PCRE

在通过grep命令使用正则表达式之前，先介绍一下它的常用参数：

参数 说明

- -b 将二进制文件作为文本来进行匹配
- -c 统计以模式匹配的数目
- -i 忽略大小写
- -n 显示匹配文本所在行的行号
- -v 反选，输出不匹配行的内容
- -r 递归匹配查找
- -A n   n为正整数，表示after的意思，除了列出匹配行之外，还列出后面的n行
- -B n   n为正整数，表示before的意思，除了列出匹配行之外，还列出前面的n行
- --color=auto 将输出中的匹配项设置为自动颜色显示
```



##### 查找命令的具体位置

```shell
# 查找 when-changed 命令包的具体位置（requests库就不行）
# 应该是针对的可以在命令行直接直接性的命令
which requests
```



### 系统信息查询

##### 查询网络连接情况

```shell
nc -vz 127.0.0.1 3306
# Connection to mysql-default 3306 port [tcp/mysql] succeeded!
```



##### 查找正在运行的进程号（Pid）

```shell
pidof `进程操作`

pidof tail  # 使用tail打开一个文件，另开一个shell，查看tail进程ID
```

##### 查看内存使用情况：**free**

```shell
free -mh  # 格式化查看内存使用情况
```



##### 显示进程信息（包括CPU、内存使用等信息）：**top、ps**



##### 查看驱动占用内存：**lsmod**



##### linux判断一个域名|IP|端口是否存

**参考：**

- https://blog.csdn.net/sun124608666/article/details/105488642
- https://blog.csdn.net/weixin_35949153/article/details/113004124

```shell
ping xxxx

$nc -zv 127.0.0.1 80
Connection to 127.0.0.1 port 80 [tcp/arepa-cas] succeeded!    # 成功
nc: connectx to 127.0.0.1 port 80 (tcp) failed: Connection refused    # 失败

wget 是一个从网络上自动下载文件的自由工具，支持通过 HTTP、HTTPS、FTP 三个最常见的 TCP/IP协议 下载，并可以使用 HTTP 代理，Linux可以用它来测试端口。
用法：wget ip:port
#出现Connecting to ... failed: Connection refused.表示端口关闭；
#出现Connecting to ... connected.表示端口开启；
#出现No route to host表示IP错误或者iptables限制。

curl是一个利用URL语法在命令行下工作的文件传输工具，可以用它来测试端口是否开启。
用法：curl -v ip:port
#出现Connection refused表示端口关闭；
#出现Connected to ip(ip) port(#0)表示端口开启；
#出现No route to host表示IP错误或者iptables限制。
```

##### 查询Linux系统最大打开的文件描述符数量

**参考：**

- [彻底弄懂 Linux 下的文件描述符（fd）](https://yushuaige.github.io/2020/08/14/%E5%BD%BB%E5%BA%95%E5%BC%84%E6%87%82%20Linux%20%E4%B8%8B%E7%9A%84%E6%96%87%E4%BB%B6%E6%8F%8F%E8%BF%B0%E7%AC%A6%EF%BC%88fd%EF%BC%89/) 

```shell
cat /proc/sys/fs/file-max 
# 或者 
sysctl -a | grep fs.file-max
```



### 快捷技巧

##### 批量杀死所有进程

```shell
ps -aux | grep python | awk '{print $2}' | xargs kill -9
```



##### 执行命令

```shell
# `echo aaa`  == echo aaa == $(echo aaa)

# 应用场景
把上条命令的执行结果作为下条命令输入
cd `which when-changed`
cat `which when-changed`
```



### Linux理论信息

##### Linux的环境变量.bash_profile .bashrc profile文件

```shell
#全局环境变量设置文件：/etc/profile、/etc/bashrc。 
#用户环境变量设置文件：~/.bash_profile、~/.bashrc。

#主要区别在于
读取顺序：① /etc/profile、② ~/.bash_profile、③ ~/.bashrc、④ /etc/bashrc。
```

.bashrc和.bash_profile的区别

```shell
.bash_profile是在你每次登录的时候执行的；.bashrc是在你新开了一个命令行窗口时执行的。
```

**应用场景**

1. 简化命令

   **参考：**

   - [(9条消息) Linux 的.bashrc 文件详解_shadow_zed的博客-CSDN博客_linux bashrc](https://blog.csdn.net/shadow_zed/article/details/88170043)

   ```shell
   alias dc='docker-compose'
   ```

   

##### fd 文件描述符

**参考：**

- [彻底弄懂 Linux 下的文件描述符（fd）](https://yushuaige.github.io/2020/08/14/%E5%BD%BB%E5%BA%95%E5%BC%84%E6%87%82%20Linux%20%E4%B8%8B%E7%9A%84%E6%96%87%E4%BB%B6%E6%8F%8F%E8%BF%B0%E7%AC%A6%EF%BC%88fd%EF%BC%89/) 

​	linux中一切都是文件，使用一个非负整数[^递增]映射文件的句柄[^正在打开的文件]，用以实现文件的高效管理。

​	标准输入0，标准输出1，标准错误2的文件描述符都指向虚拟终端控制台 /dev/pts/1

```shell
# 1. 使用vim打开一个文件，另起一个shell执行 pidof vim 查看vim的进程ID
pidof vim 
298

# 2. 使用 ll 、proc/$pid/fd 查看这个进程的文件描述符列表
ll /proc/`pidof vim`/fd

total 0
dr-x------ 2 githuyan githuyan  0 Feb  7 11:29 ./
dr-xr-xr-x 9 githuyan githuyan  0 Feb  7 11:28 ../
lrwx------ 1 githuyan githuyan 64 Feb  7 11:29 0 -> /dev/pts/1
lrwx------ 1 githuyan githuyan 64 Feb  7 11:29 1 -> /dev/pts/1
lrwx------ 1 githuyan githuyan 64 Feb  7 11:29 2 -> /dev/pts/1
lrwx------ 1 githuyan githuyan 64 Feb  7 11:29 4 -> /mnt/d/projects/owner/ymg/.demo.py.swp*

# 标准输入0，标准输出1，标准错误2的文件描述符都指向虚拟终端控制台 /dev/pts/1
# 4 指向了 demo.py文件的附件
```

指向附件的原因：

​	vim命令会先打开一个源文件，文件描述符应该为3，复制一个副本，在这个副本上操作，文件描述符应该为4，然后关闭源文件，fd=3释放，fd=4保留



### crontab命令

**参考：**

- [Linux Crontab 定时任务 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/linux-crontab-tasks.html) 



* * * * *  #分，时，日，月，周

- ***** 取值范围内的所有数字
- **/** 每过多少个数字
- **-** 从X到Z
- **，**散列数字
