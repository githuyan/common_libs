# Redis

> remote dictionary server 远程字典服务

## 注意事项

1. redis 的 hash 类型，并不能设置其中的每一个键值对的过期时间，过期时间只能是针对于这一整个字典来说，所以对于动态数据来说，当数据增加时， 我们可以保证数据在缓存中持续增加，但是当部分数据减少时，由于依然在更新缓存，所以这部分数据在缓冲中会持续存在，这就造成数据不准确

2. **redis 大键的危害**

   > [Redis大Key优化（大对象优化）总结 - 郭慕荣 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jelly12345/p/14972572.html)

   - 读写大key会导致超时严重，甚至阻塞服务。
   - 如果删除大key，DEL命令可能阻塞Redis进程数十秒，使得其他请求阻塞，对应用程序和Redis集群可用性造成严重的影响。
   - 建议每个key不要超过M级别。

# 技巧

### scan()

> 相较于 keys * ，scan基本不会阻塞，而前者属于全表扫描

**参考：** 

- https://www.jianshu.com/p/07c216db6e09

**优势：**

1. 复杂度虽然也是 O(n)，但是它是通过游标分步进行的，不会阻塞线程;
2. 提供 limit 参数，可以控制每次返回结果的最大条数，limit 只是一个 hint，返回的结果可多可少;
3. 提供模糊查询

```python
scan()
```

## 

### **同时设置键和过期时间**

**参考：** [(142条消息) 如何用Redis实现分布式锁_GeorgiaStar的博客-CSDN博客_redis实现分布式锁](https://blog.csdn.net/fuzhongmin05/article/details/119251590)

```shell
从 Redis 2.6.12 版本开始， SET 命令的行为可以通过一系列参数来修改：

SET key value EX second ：# 设置键的过期时间为 second 秒。 
SET key value PX millisecond ：# 设置键的过期时间为 millisecond 毫秒。 

NX ：# 只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 
XX ：# 只在键已经存在时，才对键进行设置操作。
```

### Redis实现分布式锁

> 容错性， 原子性

**参考：** [如何用Redis实现分布式锁_GeorgiaStar的博客-CSDN博客_redis实现分布式锁](https://blog.csdn.net/fuzhongmin05/article/details/119251590) -- 极好

1. 设置锁时应该是原子性操作，并判断锁是否存在，不能出现两个服务共享一把锁的情况

   ```js
   SETNX   == set if not exit
   
   setnx lock_key 1 EX 10 NX // 当锁不存在时加锁并设置过期时间 加锁和设置过期是一个原子性操作
   ```

2. 每个锁的键应该是独一无二的，确保删除锁时不会出现误删操作

   ```js
   //释放锁 比较unique_value是否相等，避免误释放
   if redis.get("key") == unique_value then
       return redis.del("key")
   
   // 这里获取锁做检查和删除锁又出现了非原子性操作，可以使用lua脚本解决，可以把以上逻辑写成Lua脚本，让Redis执行。因为Redis处理每个请求是单线程执行的，在执行一个Lua脚本时其它请求必须等待，直到这个Lua脚本处理完成，这样一来GET+DEL之间就不会有其他命令执行了
   ```

### Redis过期键的删除策略

> redis 本身采用定时删除+惰性删除的策略，定时批量删除过期键，零散过期键惰性删除，（将所有设置了过期时间的键统一放在一个字典里）

**参考：**

- [(145条消息) redis过期策略及配置方式_xyail66的博客-CSDN博客_redis过期策略哪里配置 ](https://blog.csdn.net/qq_27357801/article/details/107814750) 

1. **立即删除**

   立即删除能保证内存中数据的最大新鲜度，因为它保证过期键值会在过期后马上被删除，其所占用的内存也会随之释放。但是立即删除对cpu是最不友好的。因为删除操作会占用cpu的时间，如果刚好碰上了cpu很忙的时候，比如正在做交集或排序等计算的时候，就会给cpu造成额外的压力。

   而且目前redis事件处理器对时间事件的处理方式–无序链表，查找一个key的时间复杂度为O(n),所以并不适合用来处理大量的时间事件。

2. **惰性删除**

   惰性删除是指，某个键值过期后，此键值不会马上被删除，而是等到下次被使用的时候，才会被检查到过期，此时才能得到删除。所以惰性删除的缺点很明显:**浪费内存**。dict字典和expires字典都要保存这个键值的信息。

3. **定时删除**

   从上面分析来看，立即删除会短时间内占用大量cpu，惰性删除会在一段时间内浪费内存，所以定时删除是一个折中的办法。
   定时删除是：每隔一段时间执行一次删除操作，并通过限制删除操作执行的时长和频率，来减少删除操作对cpu的影响。另一方面定时删除也有效的减少了因惰性删除带来的内存浪费。

**一些需要注意的地方：**

- 设置缓存时间时尽量设置一个范围的值，而不是确定值，避免大量键同时过期。

### Redis执行lua脚本

**执行缓存脚本**

> lua脚本预加载

```shell
# 1. 先通过redis讲一段lua脚本缓存在服务器,获得一个 sha1 校验码
redis-cli script load "$(cat test.lua)"
"123456789b24c879d926f3a38cb21a3fd9062e55"

# 2. 根据给定的 sha1 校验码，执行缓存在服务器中的脚本。
raw = redis.evalsha(sha1, params)
```

```lua
# lua脚本，默认获取两个参数， KEYS ARGV-
local key = KEYS[1]
local limit = tonumber(ARGV[1])
local expire_time = ARGV[2]
 
local current = tonumber(redis.call('get', key) or "0")
if current > 0 then
    if current + 1 > limit then
        return 0
    else
        redis.call("INCR", key)
        return 1
    end
else
    redis.call("SET", key, 1)
    redis.call("EXPIRE", key, expire_time)
    return 1
end
```



**直接运行脚本**

EVAL 命令

> 接收两个参数大概是使用的类的用法
>
> EVAL script numkeys key [key ...] arg [arg ...]

```shell
script：
numkeys： # 用于指定键名参数的个数。
key [key ...]： # 全局变量 KEYS 数组，用 1 为基址的形式访问 KEYS[1]
arg [arg ...]： # 附加参数，全局变量 ARGV 数组访问，访问的形式 ARGV[1]

# 例子
eval "redis.call('set', 'name', 'tom')" 1 name tom
eval "redis.call('set', KEYS[1], ARGV[1])" 1 name tom  # KEYS 和 ARGV 必须是大写
```





# Redis理论

### 应用场景分析

适合

- 缓存
- 排行榜
- 计数器，限速器，统计在线人数，浏览量，播放量
- 好友关系，利用集合的一些命令，像是并集差集
- 简单地消息队列（订阅发布）
- session服务器，如果是集群服务，将session保存在session服务器上，无论用户访问哪个服务器都能获取信息

不适合

- 数据访问评率太低

- 数据访问量太大

常用的功能

1. 数据缓存
2. 数据持久化
3. 消息队列
4. 支持事务，redis的单条命令是原子性的，但事务不保证原子性
5. 分布式锁 session类似

### 基础理论

> 默认redis有16个数据库

- 开启一个redis客户端

  redis-cli -p 6379 开启一个redis客户端

- 切换

  select 2 切换到2号数据库

- 查询

  dbsize 查看当前数据库使用情况

  keys * 查看当前数据库的所有键，这个遍历会导致阻塞

  exists name 查询name键是否存在

  get name 获取值

  ttl name 查询当前的过期时间，

  type name 查询类型

  info replication 查询本redis服务器信息

- 删除

  flushdb 清空当前数据库

  flushall 清空所有数据库

- 过期时间

  exipre name 10 设置已存在的键name10秒后过期 -1代表没有过期时间，-2代表当前键不存在

- 系统设置

  config set requirepass " "

  config get requirepass 设置密码

  config set dir ""

  config get dir 持久化保存rdb文件路径 只要在这个路径下的rdb文件，都会被恢复到redis缓存中

### 基础使用

**过期时间**

```shell
ttl key  # 以秒为单位返回 key 的剩余过期时间
# key 不存在返回 -2
# key 存在但是没有关联超时时间返回 -1
```



### 常用类型

#### String类型

> set key value
>
> set user "{'name1':2,'name2':3}"

strlen key 查询value的长度

append key  addiaiton 附加内容 如果当前key不存在则创建一个



del key 删除某一个键



set views 0

incr views 将views的值实现 i++

​	incrby views 10 设置步长，自增10

decrviews 将views的值实现 i--



getrange key 1 -3 查看value[1,-1]范围的字符

setrange key offset value1 偏移offset位，开始一个字符一个字符的替换



setex == set + expire 

setex name 10 tom

setnx != set  

setnx key value  如果key不存在就创建，如果存在即不动作，而set会覆盖



原子性操作，批量中一个失败，都会失败

mset k1 v1 k2 v2 批量创建

mget k1 k2 批量获取

#### **List类型**

> list类型是一个双向链表

- 增

  lpush items 1 2 3 4 将元素push进列表的头部[4,3,2,1]

  rpush items 0  将元素push进列表的尾部

  lrange items 0 4  范围

- 查

  llen 列表长度

  lpop items  弹出最左值

  rpop items  弹出最右值

  lindex items 2 通过下标取值

- 删

  lremitems 2 value 移除items中的2个value 精确匹配

  rpoplpush list1 list2 	pop一个List1中的元素并Push进List2

- 改

  ltrim items 2 3 截取 items的[2,3]内容保存到items

  lset items index value 将items的[index]位的元素更新为value 

  linsert items before '2' 'world' 在items列表的'2'元素before/after插入一个'world'元素

#### **Set类型**

**无序集合**

> 无序不重复

- 增，删

  sadd myset 'hello'

  srem myset '1'   移除myset中的 1 元素

  spop myset 随机删除一个元素

- 查

  smembers myset 返回集合中的所有值

  srandmenber 随机获取一个元素

  srandmember 2 随机获取两个元素

  scard myset 获取myset集合中元素的数量

  sismember myset '1'  查看myset 中是否有(is member) ’1‘元素

- 改

  smove myset myset1 'world' 移动myset中的world元素到myset1中

- 与或非

  sdiff myset1 myset2 差集

  sinter myset1 myset2 交集

  sunion myset1 myset2 并集

  sinterstore myset3 myset1 myset2 求myset和myset2的交集并保存到myset3

**有序集合**

> 大部分操作类似，只是添加了一个优先级

1. 增，删，查

   zcard keys 统计总量

   zadd myset value score  向myset中添加一个带有score的value，根据这个socre排序

   zincrby myset age 2 自增

   zdscrby myset age 2 自减

   zrem myset value  删除value

   zremrangebysocre myset 0 10 移除集合中分数0 - 10 的所有值

   zrange myset 0 10 返回指定区间的元素

   zcount myset 0 10 找出 0 -10之间的元素数量

   zscore myset member 返回集合中指定成员的分数（优先级）

2. 排序

   zrangebyscore myset min max

   zrangebyscore myset 0 18 将0<score<18的元素升序排列

   zrevrangebyscore myset max min 根据score降序排列

   zrevrange myset max min 根据score降序排列

### Redis配置文件

> 配置文件对大小写不敏感

网络配置 NETWORK

ip绑定：bind 0.0.0.0

保护模式：protected-mode yes

端口号 ：port 6379

save 保存之前修改的配置 这个命令会自动触发持久化操作，在持久化处理完之前一直处于阻塞状态，慎用

#### **通用配置 GENERAL**

守护进程 ：daemonize yes   默认为no，若开启守护进程（后台运行）

pidfile /var/run/redis_6379.pid 如果以后台方式运行，就需要制定一个pid文件

日志级别：loglevel notice

日志文件位置名 loggile  " "

默认数据库数量：databases 16

是否总是显示Logo ：always-show-log yes

##### **快照 SNAPSSHOTTING snapshotting**

> 持久化 在规定的时间内，执行了多少次操作，就会持久化到文件 .rdb, .aof
>
> redis是内存数据库，如果没有持久化，那么断电即失

save 900 1 如果900秒内，有>=1个key被修改，那么就生成一个快照，进行持久化操作

save 60 1000 如果60秒内，有>=1000个key被修改，那么就进行持久化操作

stop-write-on-bgsave-error yes 如果持久化出错，是否继续工作

rdbcompress yes 是否压缩rdb文件（持久化的文件，压缩文件需要消耗cpu资源)

rdbchecksum yes 保存rdb文件时，是否进行错误的检查效验

dir ./   .rdb文件保存目录，这里尽量自定义文件路径

##### 主从复制  REPLICATION 复制

> 文件配置不够灵活，直接使用命令行配置

##### **安全 SECRETY**

requirepass 123456 设置密码 配置文件设置

config set requirepass 123456 命令行配置

设置密码之后，需要重新登录

auth 123456 

get requirepass

##### **限制 CLIENTS**

maxclients 1000 链接的最大客户端数量

maxmemory 《byte》 最大的内存容量

maxmemory-policy noeviction 内存满了之后的处理策略

- 返回错误

- 删除过期的key

- 删除即将过期的Key

- 随机删除key

##### **持久化文件配置**

appendonly no  默认不开启.rof模式，默认是使用rdb模式持久化

dbfilename dump.rdb 默认开启rdb模式，的持久化文件名

appendfilename 'appenddonly.aof' 持久化文件名字

appendfsync always 每次修改都会同步一次

appendfsync everysec 每隔1秒同步一次 默认

appendfsync no 不执行同步

#### **事务**

Redis单条命令是保证原子性的，但事务不保证原子性

事务 ： 一组命令的集合

一次性，顺序性，排他性

没有隔离级别的概念，所有命令在事务中（队列），并没有直接被执行，只有发起命令时才会被执行
1. 开启事务 multi
2. 命令入队
3. 执行事务exec

#### **持久化**

> Redis的数据断电即失，断电是说关闭redis服务器，而不是关闭客户端

- 配置文件

  save 60 4

  save 100 96 这个持久化规则可以有多个

- 触发持久化规则

  save 

  flushall

  shutdown

  使用kill 杀死进程不会自动触发持久化，会造成数据丢失

  恢复rdb文件内容 只要将rdb文件放在redis启动目录

- **RDB** redis database（常用）

  > 将Reids在内存中的数据库记录定时dump到磁盘上的RDB持久化

  dump.rdb

  redis 在主进程开始后，会单独创建一个子进程来进行持久化，这个子进程会将数据写入到一个临时文件，当持久化结束，再用这个临时文件替换上一个持久化好的文件，主进程不参与IO操作，所以性能很高，

  优点：用于大规模数据需要恢复，且对数据的完整性要求不高，性能极高

  缺点：在于rdb模式的最后一次持久化数据可能会丢失

- **AOF 模式** appendonly no 默认是不开启的, 默认每秒更新一次 

  > 将Reids的操作日志以追加的方式写入文件

  aof模式默认是文件的无限追加，当文件大于64mb时（auto-aof-rewrite-percentage,auto-aof-rewrite-min-size），会在fork一个新的子进程开始另一个文件

  aof模式，以日志的方式记录对数据库每一次写操作，就是一个日志文件

  如果.aof文件有错误，那么会直接无法连接

- 数据恢复

  可以使用redis-check-aof --fix appenonly.aof 恢复损坏的数据，大量数据被损坏无法恢复，

  优点：每一次数据都修复，文件的完整性会更好，从不同步效率是最高的

  缺点：每一次aof文件的更新都是IO写入操作，它的运行效率要慢于rdb，从数据文件来看，aof文件也要远远大于rdb文件，它的修复速度要慢于rdb

#### **发布与订阅**

> 发布者，订阅者，频道 队列
>
> 相当于所有的订阅者共享一块内存，发布者将消息装载进内存中，所有的订阅者都会实时得到消息

- 订阅频道

  subscribe channel1 channel2 频道3 ...

- 退订频道 用于程序

  unsubscribe channel1

- 发布消息

  publish channel1 message

使用场景

1. 实时消息系统

2. 实时聊天，频道作为聊天室

3. 订阅，关注系统

#### **主从复制**

> 读写分离 主机(master)可以读写，从机(slaves)只能读不能写

- 数据的赋值都是单向的
  - 当主机断开，从机依然可以读取之前的信息只是没有写操作了，当主机重新恢复，从机也恢复与主机的链接
  - 如果从机断开，当从机重新链接时，他会默认成为一个新的主机（需要配置），失去与源主机的链接，若重新配置为源主机的从机，那么它会重新获取所有主机的所有信息
  - 当主机断开，从机也断开，主机重连，并写入信息，之后从机在重连，此时，从机依然会获取主机写入的所有信息

- 命令设置主从关系

  - slaveof 主机ip 主机端口

  - slaveof no one  使自己成为主机

- 配置文件设置主从关系
  
- replicaof 主机ip 主机端口 	默认情况为空，即默认本机为主机
  
- 复制原理

  当一个slave启动成功并连接到一个master后会发送一个 sync（同步） 命令，

  master 接到命令，启动后台的存盘程序，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕后，master将传送整个数据文件到slave，并完成一次完全同步

  - 全量复制：slave 服务在接受到的数据库文件数据之后，将其存盘到内存中

  - 增量复制：master继续匠心的所收集到的修改命令依次传给slave，完成同步

    slave只要重新链接master，就会完成一次全量复制

- 哨兵模式（sentinel）是独立于主从进程之外的子进程，他通过发送命令，等待主机从机响应，来监控reids实例

  sentinel monitor 被监控的名称 host port 1(这是投票机制)

  sentinel monitor myredis host 127.0.0.1 6379 1

  哨兵同时监控一个主机多个从机时，若主机意外宕机，哨兵会开始默认三分钟的故障转移（failover-timeout) 时间，之后哨兵通过选举算法设置新的主机，当旧主机重新连接时，只能自动成为新主机的从机

  当设置哨兵集群时，还需要配置哨兵端口



### **缓存的问题**

> 服务器的高可用问题

##### 缓存穿透

用户想查询一个数据，发现缓存中没有（即缓存没有命中），于是向持久层数据库查询，当很多的用户同时查询，缓存都没有命中，都去请求持久层数据库，会造成数据库的压力很大，这就是缓存穿透

解决方法 

布隆过滤器

这是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行效验，不符合就丢弃，从而避免了对底层存储系统的查询压力



##### 缓存击穿

是指大量用户同时访问缓存中的key,这就是一个热键，这个热键在同一时间承受着高并发，当缓存中这个key键失效的瞬间，持续的大并发直接访问持久层数据库，造成缓存击穿，这个key键失效的原因可能是时间过期

解决方法

1. 设置缓存过期时间

2. 分布式锁



**缓存雪崩**

是指很多数据同时达到过期时间，同时失效，造成对这些数据的请求都直接指向持久层数据库，造成数据库的压力，

而当缓存服务器意外宕机，导致缓存层直接失效，这就是缓存的自然雪崩，

解决方法

1. redis的高可用，多增设几台redis服务器，搭建redis服务缓存集群
2. 限流降级，缓存失效后通过加锁或者队列来控制读数据库写缓存的线程数量，比如对某个key只允许一个线程查询，或只允许某一个线程查询，其他服务等待，或停止对这个key的操作
3. 注意**
   - 一定要修改 dump.rdb（持久化文件）的目录为绝对路径，默认不靠谱





