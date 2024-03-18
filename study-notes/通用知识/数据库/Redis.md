## Redis

> remote dictionary server 远程字典服务

#### 注意事项

1. redis 的 hash 类型，并不能设置其中的每一个键值对的过期时间，过期时间只能是针对于这一整个字典来说，所以对于动态数据来说，当数据增加时， 我们可以保证数据在缓存中持续增加，但是当部分数据减少时，由于依然在更新缓存，所以这部分数据在缓冲中会持续存在，这就造成数据不准确

2. **redis 大键的危害**

   > [Redis大Key优化（大对象优化）总结 - 郭慕荣 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jelly12345/p/14972572.html)

   - 读写大key会导致超时严重，甚至阻塞服务。
   - 如果删除大key，DEL命令可能阻塞Redis进程数十秒，使得其他请求阻塞，对应用程序和Redis集群可用性造成严重的影响。
   - 建议每个key不要超过M级别。

### 技巧

##### 在不修改更新时间的情况下，更新键的值

> 使用`SET`命令的`XX`选项（仅在键已经存在时才设置值），仅适用于Redis版本2.6.12及更高版本。

```shell
set name tom XX
```



##### scan()

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



##### **同时设置键和过期时间**

**参考：** [(142条消息) 如何用Redis实现分布式锁_GeorgiaStar的博客-CSDN博客_redis实现分布式锁](https://blog.csdn.net/fuzhongmin05/article/details/119251590)

```shell
从 Redis 2.6.12 版本开始， SET 命令的行为可以通过一系列参数来修改：

SET key value EX second ：# 设置键的过期时间为 second 秒。 
SET key value PX millisecond ：# 设置键的过期时间为 millisecond 毫秒。 

NX ：# 只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 
XX ：# 只在键已经存在时，才对键进行设置操作。（不变动过期时间）
```

##### 分布式锁

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

##### 过期键的删除策略

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



### Redis理论

#### 应用场景分析

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

#### 基础理论

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

##### 不当的命令会造成阻塞

- key *：所有key
- hgetall：返回哈希表中的所有成员
- smembers: 返回集合中的所有成员
- flashdb



#### 基础使用

##### 过期时间

```shell
ttl key  # 以秒为单位返回 key 的剩余过期时间
# key 不存在返回 -2
# key 存在但是没有关联超时时间返回 -1
```

#### 运行脚本

##### EVAL 命令

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



##### lua脚本预加载

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





##### 管道与事务

**参考：**

- https://rafaeleyng.github.io/redis-pipelining-transactions-and-lua-scripts 
- [【Redis】Redis 事务和事务锁](https://juejin.cn/post/7292036126268997642?share_token=e44bf34a-f82c-4d9f-8fdf-81c1e274ba4b) 

redis事务是原子性的，且阻塞的，redis本身不提供回滚机制

```shell
MULTI        # 开始一个事务
SET key1 10  # 将命令添加到事务队列
GET key1     # 将命令添加到事务队列
DISCARD | EXEC      # 清空事务队列，并取消事务 | 执行事务
SET key3 30  # 这个命令是在事务之外执行的，不受事务的影响
EXEC         # 事务已经结束，报错：ERR EXEC without MULTI
```

未完成的事务不会被持久化

AOF: AOF（Append-Only File）持久化模式会记录每一条**非查询操作原子**命令

RDB: 保存每一个原子操作后的快照

```shell
1. MULTI  
2. SET key1 10 
3. GET key1 
4. DISCARD  
5. SET key2 20
6. EXEC      
```

在事务未完成之前，不会记录在日志中（aof, rdb)，事务执行过程中，redis服务断开，不会保存未完成事务的内容



#### 常用类型

> redis在设置一个已存在的键时，且类型不一致时，会报错：WRONGTYPE Operation against a key holding the wrong kind of value，在设置键时应该先检查空值，在进行覆盖操作，
>
> 使用 **TYPE your_key** 检查键类型

**参考：**

- [redis核心原理与实战](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/Redis%20%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E5%AE%9E%E6%88%98/14%20%E6%9C%89%E5%BA%8F%E9%9B%86%E5%90%88%E4%BD%BF%E7%94%A8%E4%B8%8E%E5%86%85%E9%83%A8%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86.md) 

在 Redis 中，`EX`（过期时间）、`NX`（只在键不存在时设置值）、和 `XX`（只在键已经存在时设置值）这些选项主要用于 `SET` 命令，

XX：仅更新已存在的元素。不要添加新元素。

NX：仅添加新元素。不要更新现有元素。

##### String类型

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

##### List类型

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

  lrem items 2 value 移除items中的2个value 精确匹配

  rpop lpush list1 list2 	pop一个List1中的元素并Push进List2

- 改

  ltrim items 2 3 截取 items的[2,3]内容保存到items

  lset items index value 将items的[index]位的元素更新为value 

  linsert items before '2' 'world' 在items列表的'2'元素before/after插入一个'world'元素

##### Set类型

> 仅支持 nx，xx，不支持使用ex

**参考：**

- [ZADD | Redis --- 扎德 |重复](https://redis.io/commands/zadd/) 



##### 无序集合

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

##### 有序集合

> 大部分操作类似，只是添加了一个优先级

**CRUD**

```shell
zadd name score value score value..  # 添加元素并指定分数
zadd name nx score value score value..  # 添加元素并指定分数，当元素存在时，使用旧的score,否则使用新的score

zrem name tom jerry..  # 删除指定元素， 删除命令中如果包含了不存在的元素，并不会影响命令的正常执行，不存在的元素将会被忽略。响应0,删除成功则响应1

zpopmax name  # 删除指定key最大的元素
zpopmin name  # 删除指定key最小的元素

# （左闭右闭）
zrange name 0 100  # 按照score正序，取指定索引范围元素
zrevrange name 0 100  # 按照socre倒序，取指定索引范围元素 
zrange name 0 100 withscores  # 按照score正序，取指定索引范围元素和元素的分数 
zrevrange name 0 100 withscores  # 按照socre倒序，取指定索引范围元素和元素的分数 

zcount name 0 100  # 获取指定分数范围内元素的数量（左闭右闭）

# 获取指定分数范围内的元素
ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
	key 是有序集合的键。
    min 和 max 分别是分数的最小值和最大值，用于指定范围。
    WITHSCORES 选项用于同时获取成员及其分数。
    LIMIT 选项用于限制返回的结果数量。
zrangebyscore name 0 100 withscores limit 0 5  # 根据分数排序，获取前5个元素和元素分数


zscore name tom  # 获取指定元素的score
zrank name tom  # 获取指定元素的索引
zcard name # 查询键的所有元素的数量
```

`zadd name [ch] score value`

- **XX**:  仅更新已存在的元素。不要添加新元素。

- **NX**:  仅添加新元素。不要更新现有元素。

- **LT**:  仅当新分数低于当前分数时才更新现有元素。此标志不会阻止添加新元素。

- **GT**:  仅当新分数大于当前分数时才更新现有元素。此标志不会阻止添加新元素。

- **CH**:  change的简写，返回本次修改中元素**分数变更**的元素数量

- **INCR**:  修改指定元素的分数， 指定 `ZADD` 此选项时，其行为类似于 `ZINCRBY` 。在此模式下**只能指定一个**分数元素对。

  > 这个操作是原子性的，即使是批量修改

  `zincrby name 100 tom` 响应name键的tom元素增加100后的分数



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

  > 将redis在内存中的数据库记录定时dump到磁盘上的RDB持久化
  >
  > RDB（Redis Database Backup）持久化模式在 Redis 中用于创建数据库的快照，将 Redis 数据以**二进制**格式保存到磁盘上。RDB 文件包含了一个 Redis 数据库的快照，它是一个经过压缩和优化的二进制文件，用于在 Redis 服务器重新启动时恢复数据。

  RDB 文件不同于 AOF 文件，它并不记录每个写操作的历史，而是在周期性的时间点上创建一个数据库快照。这样，当 Redis 服务器因为某种原因重新启动时，可以更快地加载整个数据库状态。

  redis 在主进程开始后，会单独创建一个子进程来进行持久化，这个子进程会将数据写入到一个临时文件，当持久化结束，再用这个临时文件替换上一个持久化好的文件，主进程不参与IO操作，所以性能很高，

  

  优点：用于大规模数据需要恢复，且对数据的完整性要求不高，性能极高

  缺点：在于rdb模式的最后一次持久化数据可能会丢失

  ```shell
  # RDB配置
  save 900 1       # 在900秒内，如果至少有1个key被改变，则持久化
  save 300 10      # 在300秒内，如果至少有10个key被改变，则持久化
  save 60 10000    # 在60秒内，如果至少有10000个key被改变，则持久化
  ```

  

- **AOF 模式** appendonly no 默认是不开启的, 默认每秒更新一次 

  > 将redis的操作日志以追加的方式写入文件， AOF（Append-Only File）持久化模式会记录每一条**非查询操作原子**命令

  aof模式默认是文件的无限追加，当文件大于64mb时（auto-aof-rewrite-percentage,auto-aof-rewrite-min-size），会在fork一个新的子进程开始另一个文件

  aof模式，以日志的方式记录对数据库每一次写操作，就是一个日志文件

  如果.aof文件有错误，那么会直接无法连接

- 数据恢复

  可以使用redis-check-aof --fix appenonly.aof 恢复损坏的数据，大量数据被损坏无法恢复，

  优点：每一次数据都修复，文件的完整性会更好，从不同步效率是最高的

  缺点：每一次aof文件的更新都是IO写入操作，它的运行效率要慢于rdb，从数据文件来看，aof文件也要远远大于rdb文件，它的修复速度要慢于rdb
  
  ```shell
  # AOF配置
  appendonly yes              # 开启AOF持久化
  appendfilename "appendonly.aof"  # AOF文件的名字
  ```
  
  AOF同步有三个选项：
  
  > 当AOF使用`always`或`everysec`选项时，同步操作可能会引起主进程的阻塞。这是因为在同步期间，主进程会等待写入操作完成同步，以确保数据已经持久化到磁盘
  
  1. **always：** 每个写命令都同步到AOF文件和磁盘上。这是最安全的选项，但也是最慢的，因为每个写操作都会引起磁盘的I/O。
  2. **everysec：** 每秒同步一次，将写入操作积累在内存中，然后每秒将它们一次性同步到AOF文件和磁盘。这是一个折中的选项，可以提供不错的性能和较高的持久性。
  3. **no：** Redis不会主动同步AOF文件，而是依赖于操作系统的缓冲机制。这是最快的选项，但可能会在断电等异常情况下导致数据丢失。

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



## redis配置项

##### 默认配置

默认服务位置： `usr/local/bin`

查看配置信息

```shell
# 查看aof启用情况
redis-cli config get appendonly
```



