### Temporary failure resolving 'mirrors.aliyun.com'

**问题描述**

```shell
在 sudo apt update 时，出现 Temporary failure resolving 'mirrors.aliyun.com'， 在 ping ww.baidu.com 时也出现同样的问题， 系统 /etc/init.d/network 文件找不到
```

**环境：** wsl2 window 的 linux子系统

**参考：**

1. https://www.cnblogs.com/zhenhunfan2/p/14230201.html
2. https://blog.csdn.net/muzihuaner/article/details/122891786

**解决方案**

1. 修改DNS

   ```shell
   sudo vim /etc/systemd/resolved.conf
   
   [Resolve]
   DNS=8.8.8.8
   #FallbackDNS=
   #Domains=
   #LLMNR=no
   #MulticastDNS=no
   #DNSSEC=no
   #Cache=yes
   #DNSStubListener=yes
   ```

2. 修改配置文件

   ```shell
   sudo vim /etc/resolv.conf 
   
   nameserver 8.8.8.8
   nameserver 8.8.4.4
   ```

3. 重启ubuntu

   > win+x a  打开window power shell

   ```shell
   net stop LxssManager
   net start LxssManager
   ```

4. 更新

   ```sehll
   sudo apt update
   ```

   

   



