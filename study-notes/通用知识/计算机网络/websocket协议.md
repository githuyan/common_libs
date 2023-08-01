#### 面向连接

> 连接是一个对象，两个进行TCP通信的服务，在进行连接前，需要对这条握手产生的连接进行配置，如超时时间、等，



#### socket和websocket的区别

**参考：**

- [**socket和websocket的区别**](https://juejin.cn/post/6844903805797007374) 

socket是位于传输层与应用层之间的一个抽象层，它不是协议，而是一组并与操作TCP和UDP的操作接口

websocket是位于应用层的一个C/S架构支持双向传输的通信协议

#### socker编程

**参考：**

- [socket编程](https://www.liujiangblog.com/course/python/76) 

<img src="../../../resource/1762677-20201007160746044-1258982359.png" alt="image.png-58.5kB" style="zoom:80%;" />

| 方法                  | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| **服务器端方法**      |                                                              |
| **s.bind()**          | 绑定地址（host,port）到套接字，在AF_INET下,以元组（host,port）的形式表示地址。 |
| **s.listen(backlog)** | 开始监听。backlog指定在拒绝连接之前，操作系统可以挂起的最大连接数量。该值至少为1，大部分应用程序设为5就可以了。 |
| **s.accept()**        | 被动接受客户端连接,(阻塞式)等待连接的到来，并返回（conn,address）二元元组,其中conn是一个通信对象，可以用来接收和发送数据。address是连接客户端的地址。 |

| **客户端方法**                       |                                                              |
| ------------------------------------ | ------------------------------------------------------------ |
| **s.connect(address)**               | 客户端向服务端发起连接。一般address的格式为元组（hostname,port），如果连接出错，返回socket.error错误。 |
| s.connect_ex()                       | connect()函数的扩展版本,出错时返回出错码,而不是抛出异常      |
| **公共方法**                         |                                                              |
| **s.recv(bufsize)**                  | 接收数据，数据以bytes类型返回，bufsize指定要接收的最大数据量。 |
| **s.send()**                         | 发送数据。返回值是要发送的字节数量。                         |
| **s.sendall()**                      | 完整发送数据。将数据发送到连接的套接字，但在返回之前会尝试发送所有数据。成功返回None，失败则抛出异常。 |
| s.recvform()                         | 接收UDP数据，与recv()类似，但返回值是（data,address）。其中data是包含接收的数据，address是发送数据的套接字地址。 |
| s.sendto(data,address)               | 发送UDP数据，将数据data发送到套接字，address是形式为（ipaddr，port）的元组，指定远程地址。返回值是发送的字节数。 |
| **s.close()**                        | 关闭套接字，必须执行。                                       |
| s.getpeername()                      | 返回连接套接字的远程地址。返回值通常是元组（ipaddr,port）。  |
| s.getsockname()                      | 返回套接字自己的地址。通常是一个元组(ipaddr,port)            |
| s.setsockopt(level,optname,value)    | 设置给定套接字选项的值。                                     |
| s.getsockopt(level,optname[.buflen]) | 返回套接字选项的值。                                         |
| s.settimeout(timeout)                | 设置套接字操作的超时期，timeout是一个浮点数，单位是秒。值为None表示没有超时期。一般，超时期应该在刚创建套接字时设置，因为它们可能用于连接的操作（如connect()） |
| s.gettimeout()                       | 返回当前超时期的值，单位是秒，如果没有设置超时期，则返回None。 |
| s.fileno()                           | 返回套接字的文件描述符。                                     |
| s.setblocking(flag)                  | 如果flag为0，则将套接字设为非阻塞模式，否则将套接字设为阻塞模式（默认值）。非阻塞模式下，如果调用recv()没有发现任何数据，或send()调用无法立即发送数据，那么将引起socket.error异常。 |
| s.makefile()                         | 创建一个与该套接字相关连的文件                               |

Python3以后，socket传递的都是**bytes类型**的数据，字符串需要先转换一下，`string.encode()`即可；另一端接收到的bytes数据想转换成字符串，只要`bytes.decode()`一下就可以。