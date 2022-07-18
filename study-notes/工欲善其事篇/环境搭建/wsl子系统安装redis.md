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

   

