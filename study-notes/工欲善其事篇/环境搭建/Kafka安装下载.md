#### Kafka安装

**参考：** [(8条消息) Kafka| 下载与安装_YvesHe的博客-CSDN博客_kafka下载](https://yveshe.blog.csdn.net/article/details/88575491?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1-88575491-blog-108452107.pc_relevant_multi_platform_featuressortv2removedup&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1-88575491-blog-108452107.pc_relevant_multi_platform_featuressortv2removedup&utm_relevant_index=1)



1. ##### 安装kafka

```shell
cd /opt
sudo wget http://mirrors.bfsu.edu.cn/apache/kafka/3.2.0/kafka_2.13-3.2.0.tgz
sudo tar -zxvf kafka_2.13-3.2.0.tgz
```

2. 安装java

   > 注意版本

   **参考：** [(8条消息) jdk_8u201_x64下载直链_DeweyLau的博客-CSDN博客_jdk-8u191](https://blog.csdn.net/DeweyLau/article/details/109636906)

   ```shell
   sudo wget https://mirrors.huaweicloud.com/java/jdk/8u201-b09/jdk-8u201-linux-x64.tar.gz
   sudo tar -zxvf jdk-8u201-linux-x64.tar.gz
   
   
   ### /etc/profile 末尾添加
   export JAVA_HOME=/opt/jdk1.8.0_201
   export PATH=$PATH:$JAVA_HOME/bin
   ###
   
   source /etc/profile
   
   java -version  # 测试
   ```

3. 配置kafka的java路径

   ```shell
   # kafka默认读取java路径是 /usr/bin/java ,所以设置一个软连接
   ln -s /JAVA_HOME/bin/java /usr/bin/java
   ```

4. 启动

   **参考：**

   - [(8条消息) Kafka安装启动入门教程_董可伦的博客-CSDN博客_kafka服务启动](https://blog.csdn.net/dkl12/article/details/80413688) 

   ```shell
   # 开一个窗口，启动zookeeper
   sudo bin/zookeeper-server-start.sh config/zookeeper.properties
   
   # 开一个窗口，启动kafka
   sudo bin/kafka-server-start.sh config/server.properties
   
   # 创建一个topic
   sudo bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic test --partitions 2 --replication-factor 1
   # 查看topic列表
   sudo bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
   ```

   - zookeeper启动错误

     ```python
     # INFO ZooKeeper audit is disabled. (org.apache.zookeeper.audit.ZKAuditProvider)
     
     # 解决
     # https://blog.csdn.net/weixin_43257585/article/details/125048124
     kafka/config/zookeeper.properties 文件中增加 audit.enable=true
     ```

   单机开启多kafka实例（多broker）

   **参考：**
   
   - [Kafka之——单机多broker实例集群搭建_冰河的技术博客_51CTO博客](https://blog.51cto.com/binghe001/5246556) 
   
   ```shell
   1. 拷贝一份新的 server.properties 文件
   2. 修改 broker_id, 修改重复的端口号
   broker.id=1
   listeners=PLAINTEXT://:9093
   log.dir=/tmp/kafka-logs_1
   ```
   
   
   
   
   
   
   
   









