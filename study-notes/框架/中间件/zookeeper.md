## zookeeper操作命令

**参考：**

- [(14条消息) zookeeper 操作命令 zkCli 简介_whatday的博客-CSDN博客_zkcli命令](https://blog.csdn.net/whatday/article/details/125239778) 

#### 基础配置



#### 踩坑

1. zookeeper容器中启动zookeeper服务失败

   ```shell
   # 报错信息
   [main-SendThread(localhost:2181):ClientCnxn$SendThread@1029] - Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
   
   # 解决方案
   ./zkCli.sh  -timeout 5000  -r  -server  172.30.0.2:2181 # zk启动命令
   
   后面的IP:Port即使在容器中也需要在docker外配置的容器网络
   docker network ls # 查看网络
   docker inpsect network netID
   ```

   

   
