#### 批量杀死所有进程

ps -aux | grep python | awk '{print $2}' | xargs kill -9



#### 基础命令

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



#### linux判断一个域名|IP|端口是否存

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



#### xargs  命令传递（管道）

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



#### 系统信息查询

- 查看内存使用情况：**free**
- 显示进程信息（包括CPU、内存使用等信息）：**top、ps**
- 查看驱动占用内存：**lsmod**

系统内存信息查询

```shell
free -mh  # 格式化查看内存使用情况
```



#### 查找命令的具体位置

```shell
# 查找 when-changed 命令包的具体位置（requests库就不行）
# 应该是针对的可以在命令行直接直接性的命令
which requests
```





#### 执行命令

```shell
# `echo aaa`  == echo aaa == $(echo aaa)

# 应用场景
把上条命令的执行结果作为下条命令输入
cd `which when-changed`
cat `which when-changed`
```



#### Linux的环境变量.bash_profile .bashrc profile文件

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

   
