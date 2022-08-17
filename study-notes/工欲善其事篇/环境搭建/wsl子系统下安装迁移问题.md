### wsl2下安装redis

1. 正常安装redis

   **参考：** https://developer.aliyun.com/article/764565

   ```
   sudo apt update
   sudo apt install redis-server
   ```

2. 启动redis

   ```shell
   # 直接使用 systemctl 会报错
   glc@LAPTOP-LEMON:~$ sudo sudo systemctl status redis-server
   System has not been booted with systemd as init system (PID 1). Can't operate.
   Failed to connect to bus: Host is down
   
   # 解决方案
   https://segmentfault.com/a/1190000040670856
   
   # 更换命令，用SysV init的命令代替systemd的
   sudo service redis-server start
   ```

3. 验证redis

   ```shell
   redis-cli  # 进入redis控制台
   ```





### wsl2下迁移ubuntu20.04

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

   

