Go安装

参考：[linux下Go环境安装_linux 安装 go_独行侠_阿涛的博客-CSDN博客](https://blog.csdn.net/wltsysterm/article/details/119383406)



地址： https://go.dev/dl/go1.21.4.linux-amd64.tar.gz



1. 下载压缩包

   ```shell
   wget https://go.dev/dl/go1.21.4.linux-amd64.tar.gz
   ```

2. 解压到linux的`usr/local`目录

   ```shell
   tar -C /usr/local -zxvf go1.21.4.linux-amd64.tar.gz
   ```

3. 配置环境变量

   ```shell
   # 在/etc/profile文件末尾增加
   export GOROOT=/usr/local/go
   export PATH=$PATH:$GOROOT/bin
   
   # 刷新最新配置
   source /etc/profile
   ```

4. 测试

   ```shell
   go version
   ```

   