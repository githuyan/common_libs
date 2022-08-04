# kafka

> 一个分布式流处理框架，用于实时构建流处理应用，经常被应用为企业级消费引擎。

## 理论知识

### 性能优化

1. 优化大量的小型I/O操作

   产生了消费者组的概念，假设我们在处理大量的web活动数据，通过合理的将消息分组，是的网络请求将多个消息打包成一组，而不是每次发送一条消息，Consumer每次获取多个大型有序的消息块，批处理操作使得允许更大的数据包，更大的顺序读写磁盘操作，和连续的内存块，讲这些顺序写入磁盘，获得性能提升。

2. 优化字节拷贝

   消息量极大时就会对性能产生影响。通过格式化消息的流动，从producter，broker，consumer 都共享标准化的二进制消息格式，这样消息的传递就不需要修改。解决字节拷贝的问题。

3. 使用零拷贝技术

### Push or Pull

> consumer获取数据的方式

**Push-base**

> producter  --push--> broker --push-->consumer

- **优点**
  1. consumer能以尽可能的最大速率消费
  2. 由于 broker 控制着数据传输速率， 所以 push-based 系统很难处理不同的 consumer
- **缺点：**
  1. 可能会使consumer不堪重负（拒绝服务攻击）

**Pull-base**

> producter  --push--> broker,  consumer --pull-->broker

- **优点：**

  1. consumer占据主导，可以自己选择消费何种类型的消息
  2. producter可以足够快，broker可以出现消息挤压，consumer后面在空闲的时候消费

- **缺点：**

  1. 如果broker中没有数据，consumer可能会出现busy轮询，直到有消息待消费，

     > 这里可以在消费时加参数，设置 long pull，

### 一些问题

1. 解决消息确认机制的问题，

   移位（offset），传统的消息确认机制包括，consumer从broker中获取消息进行消费，broker更新消息状态为send， consumer消费结束后返回标志符，broker再更新消息装填为consumed，但是这个过程可能会出现大量的消息不一致和性能问题：

   - 重复消费，consumer处理了消息，但是发送标志的时候出错
   - broker存储大量的状态（为了保持一致性，需要加锁），造成性能浪费

   解决方案：

   每个topic的分区下的每条消息都被分配一个位移的ID数值，用于表示他在分区中的位置

2. 每个分区在任意时间只能有一个消费者进行消费

## 基础使用

### Topic操作





### 消费组者组操作

#### 移位重设

> 通过移位重设实现了消息确认机制， 在第一个消费者创建的时候设置移位

**参考：**

- [Kafka 消费者组位移重设的几种方式 - 掘金 (juejin.cn)](https://juejin.cn/post/7034447850066935844) 

移位重设策略：

- **earliest**

  > 把位移重设到当前最早位移处。
  >
  > **注意：**  Kafka 会删除较早的日志，因此，最早的位置不一定是 0

  ```python
  bin/kafka-consumer-groups.sh --bootstrap-server <host>:<port> --group <group_id> --reset-offsets --all-topics --to-earliest –execute
  ```

- **latest**

  > 把位移重设到当前最新位移处。跳过所有历史消息，从最新的消息开始消费

  ```python
  bin/kafka-consumer-groups.sh --bootstrap-server <host>:<port> --group <group_id> --reset-offsets --all-topics --to-latest –execute
  ```

- **current**

  > 把位移重设到当前最新提交的位移处。??

  ```python
  bin/kafka-consumer-groups.sh --bootstrap-server <host>:<port> --group <group_id> --reset-offsets --all-topics --to-current –execute
  ```

- **specified-offset  和  Shift-By-N**

  > 把位移重设到一个指定的位移处。或者一个相对的位移（+n)
  >
  > 用于某一段时间消息因外部原因无法消费，需要跳过的场景
  >
  > **注意：** 这里的 n 也可以是负数，表示之前。

  ```python
  # --to-offset <offset>
  bin/kafka-consumer-groups.sh --bootstrap-server <host>:<port> --group <group_id> --reset-offsets --all-topics --to-offset 100 –execute
      
  #  --shift-by <offset_N>
  bin/kafka-consumer-groups.sh --bootstrap-server <host>:<port> --group <group_id> --reset-offsets --all-topics --to-shift-by -100 –execute
  ```

- **dateTime** 

  > 把位移重设为指定时间之后第一个位置

  ```python
  # 这个时间格式可能有点问题？？
  bin/kafka-consumer-groups.sh --bootstrap-server <host>:<port> --group <group_id> --reset-offsets --all-topics --to-datetime 2021-11-25T20:00:00.000 –execute
  ```

- **duration**

  > 把位移重设为与当前时间相对的一个时间点之后的第一个位置。（相对时间）

  ```python
  # 对于 Duration 策略，需要提供一个符合 ISO-8601 规范的 Duration 格式，以字母 P 开头，后面由 4 部分组成，即 D、H、M 和 S，分别表示天、小时、分钟和秒
  bin/kafka-consumer-groups.sh --bootstrap-server <host>:<port> --group <group_id> --reset-offsets --all-topics --by-duration PT0H30M0S –execute
  ```

#### 消息交付语义

**参考：**

- [(8条消息) Kafka的三种语义_简单随风的博客-CSDN博客_kafka三种语义](https://blog.csdn.net/lt326030434/article/details/119881907) 

##### at-last-one

> 至少一次，保证消息一定会被消费，但可能会重复消费
>
> 消费者的消息处理完并输出到结果库，但是offset还没提交，这个时候消费者挂掉了，再重启的时候会重新消费并处理消息，所以至少会处理一次



#### at-most-once

> 至多一次， 保证不会重复消费，但可能会丢失消息
>
> 消费者的offset已经提交，但是消息还在处理中(还没有处理完)，这个时候程序挂了，导致数据没有被成 功处理，再重启的时候会从上次提交的offset处消费，导致上次没有被成功处理的消息就丢失了

```python
# 改配置
# 这个配置使得kafka会有一个独立的线程负责按照指定间隔提交offset
enable.auto.commit = true
auto.commit.interval.ms=10  # 设置一个较小的时间范围
```

#### exactly-once

> 恰好一次， 完美的方式
>
> 以原子事务的方式保存offset和处理的消息结 果，这个时候相当于自己保存offset信息了，把offset和具体的数据绑定到一块，数据真正处理成功的时 候才会保存offset信息



#### replication: leader-follower

> 不同于主从复制的另一种同步方法











