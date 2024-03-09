#### 技巧

##### 增加内存

添加vm.max_map_count，

1. 临时修改:

   ```shell
   # 使用命令, 这会立即修改当前运行系统的该参数值,但重启后会失效
   sudo sysctl -w vm.max_map_count=262144
   ```

2. 永久修改:

   ```shell
   # 这会把 /etc/sysctl.conf 中的配置应用到系统中,重启后也会保留
   # a) 编辑 /etc/sysctl.conf 文件:
   sudo vim /etc/sysctl.conf
   
   # b) 在文件末尾添加:
   vm.max_map_count = 262144
   
   # c) 保存并退出。
   
   # d) 执行命令让修改生效:
   sudo sysctl -p
   ```

#### wsl2备份

> wsl2的备份有些鸡肋，只能备份文件系统，无法备份一些安装的组件，也就是说，组件的相关旧配置都存在，组件本身需要重新安装，需要注意跟之前配置的旧版本信息对应

**参考：**

- [如何对WSL2进行备份与还原](https://zhuanlan.zhihu.com/p/536686989) 

**备份**

```shell
# 确定自己的版本
wsl -l -v 
wsl --export Ubuntu20.04 ./system_bak/wsl2.tar
```

**还原**

```shell
wsl --import Ubuntu c:\wsl2 d:\system_bak\wsl2.tar
```



#### wsl2下迁移ubuntu20.04

**参考：**

- [(10条消息) Windows10子系统Ubuntu20.04LTS安装并迁移至非系统盘_wrdoct的博客-CSDN博客_windows子系统迁移](https://blog.csdn.net/XUfengge111/article/details/123682429) 
- [(10条消息) 【转】搞机：window10安装Linux子系统（WSL）及迁移到非系统盘_sinolover的博客-CSDN博客](https://blog.csdn.net/sinolover/article/details/120922454) -- 更好

1. 下载迁移工具

   [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline/releases) 

2. 查看当前wsl2绑定的系统

   ```python
   F:\tools\ubuntu_wsl_migrate>.\LxRunOffline.exe list
   Ubuntu-20.04
   ```

3. 使用 `lxrunoffline move` 进行迁移 ， -n 指定你要迁移的系统名 ，-d 指定你新系统的迁移路径

   ```python
   .\LxRunOffline.exe move -n Ubuntu20.04LTS -d D:\Ubuntu20.04LTS
   ```

4. 使用`LxRunOffline.exe get-dir` 查询系统目录

   ````python
   F:\tools\ubuntu_wsl_migrate>.\LxRunOffline.exe get-dir -n Ubuntu-20.04
   F:\Ubuntu-20.04
   ````




#### 重启wsl2

```shell
#停止LxssManager服务
net stop LxssManager  
 
#启动LxssManager服务
net start LxssManager  

```



