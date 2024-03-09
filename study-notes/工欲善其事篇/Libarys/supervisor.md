#### supervisor配置

##### supervisor安装

要在 Linux 系统上安装 Supervisor，可以按照以下步骤进行操作：

1. 使用包管理器安装 Supervisor：
   - 在 Ubuntu 上，可以使用以下命令安装：
     ```shell
     sudo apt update
     sudo apt install supervisor
     ```
   - 在 CentOS 上，可以使用以下命令安装：
     ```shell
     sudo yum install epel-release
     sudo yum install supervisor
     ```

2. 启动 Supervisor 服务：
   ```shell
   sudo systemctl start supervisor
   ```

3. 设置 Supervisor 开机自启动：
   ```shell
   sudo systemctl enable supervisor
   ```

4. 检查 Supervisor 服务状态：
   ```shell
   sudo systemctl status supervisor
   
   # 成功示例
   ● supervisor.service - Supervisor process control system
      Loaded: loaded (/lib/systemd/system/supervisor.service; enabled; vendor preset: enabled)
      Active: active (running) since Wed 2022-12-14 15:25:42 UTC; 10min ago
        Docs: http://supervisord.org
     Process: 12345 ExecStart=/usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf (code=exited, status=0/SUCCESS)
    Main PID: 12345 (supervisord)
      CGroup: /system.slice/supervisor.service
              ├─12345 /usr/bin/python /usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf
              └─67890 /usr/bin/python /usr/bin/some_program.py
   
   Dec 14 15:25:42 yourhostname systemd[1]: Starting Supervisor process control system...
   Dec 14 15:25:42 yourhostname systemd[1]: Started Supervisor process control system.
   ```

一旦完成以上步骤，Supervisor 就会成功安装并运行在你的系统上。你可以通过编辑配置文件 `/etc/supervisor/supervisord.conf` 来配置 Supervisor 的相关参数和管理进程。



配置一个sh脚本后台运行

1. 创建一个脚本

   ```bash
   # sh脚本必须指定头文件
   #!/bin/bash
   ```

2. 配置supervisor管理文件

   ```shell
   # /etc/supervisor/conf.d/k8s-run.conf
   
   [program:k8s-run]
   command=/mnt/d/projects/servers/k8s/k8s-run.sh  # 这里替换为你的 k8s-run.sh 脚本的路径
   # sh 脚本不需要指定解释器，python脚本需要指定解释器
   # command=/usr/bin/python3 /path/to/run.py  # 这里替换为你的 run.py 脚本的路径
   
   directory=/path/to/directory  # 这个选项用于指定运行命令的目录。如果脚本需要在特定的工作目录下运行，你可以设置此选项。如果不设置，默认情况下将在 Supervisor 启动时的当前工作目录下执行命令。
   autostart=true # 指定是否在 Supervisor 启动时自动启动该程序
   autorestart=true  # 指定在程序异常退出时是否自动重启
   startretries=3  # 指定 Supervisor 尝试重新启动该程序的次数
   user=your_username  # 指定运行程序的用户。你可以将程序的运行权限限制为特定用户
   stderr_logfile=/var/log/run-k8s.err.log  # 指定了标准错误输出的日志文件路径
   stdout_logfile=/var/log/run-k8s.out.log  # 指定了标准输出的日志文件路径
   ```

3. 重新加载supervisor

   ```shell
   sudo supervisorctl reread
   sudo supervisorctl update
   
   sudo systemctl status supervisor
   ```

4. 启动脚本

   ```shell
   # alias sus='sudo supervisorctl'
   
   sus start k8s-run
   ```

   

##### supervisor使用

```shell
# alias sus='sudo supervisorctl'

sus status

sus stop k8s-run

sus restart k8s-run

```

