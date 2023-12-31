# SQLServer数据的安装

## 删除

> #### 血泪啊，删除一定要删干净

## ==尽量不要改注册表，就不要改注册表，不要觉得网上的都能适合自己==

1. #### 安装失败后，停止安装，找到控制面板，删除所有sql server相关的所有文件，

   一个一个右键删除，急不得，一般情况都可以删除，如果当时不能删除这个文件，就删下一个，把这个放在最后删，其中一个文件是卸载文件，删除的时候会有提示，需要一段时间才能删干净

   ![image-20210724181921718](../../resource/image-20210724181921718.png)

2. #### 打开任务管理器

   杀死所有的sql server 相关的进程

3. #### 删除这个文件 `C:\Program Files\Microsoft SQL Server`,

   注意，如果上面几步没有删干净，这里就会显示文件已被打开，无法删除，如果已经删干净了，会提示需要管理员权限才能删除

4. #### 删除这个文件`C:\Program Files (x86)\Microsoft SQL Server`，（如果有的话）

5. #### 删除自己定义的sql server 的一些目录，文件

6. #### 在开始菜单里所有sql ，删除所有与sql server相关的文件

   比如：

   ![image-20210724183032274](../../resource/image-20210724183032274.png)

   这样的话，一般的情况下就删除干净到可以重新安装的地步了，实在不放心的话，可以在重启一下，（而且，如果之前有没有删除的文件，可以在重启后删除成功）

## 安装

1. ### 首先下载sqlserver数据库2014，管理员身份打开文件，

   **全新的sql server 独立安装或向现有安装添加功能**

   可以现在工具里面检测一下环境，后期安装过程中，有任何错误，安装都不会成功

   ![image-20210724180926802](../../resource/image-20210724180926802.png)

2. ### 产品密匙

   **这里可以直接百度搜索产品密匙，也可以指定版本使用，好像是90天**

   ![image-20210724181248084](../../resource/image-20210724181248084.png)

3. ### 接受条款

   **之后会有全局规则，如果都满足会直接跳到下一部分，若不满足，则会有状态报警**

   出错的话，最好是直接删干净重新安装（很麻烦）

   ![image-20210724181431293](../../resource/image-20210724181431293.png)

   ![image-20210724181444213](../../resource/image-20210724181444213.png)

4. ### 一直下一步

   **到这里，全选就好**

   ==后面那三个目录前两个要在同一个目录，而且都是可以设置的，如果你的有任何一个或两个是灰色的，无法设置，那说明，你之前下载过sql server 并且上一个的参与文件没有删除干净==

   ![image-20210724185730868](../../resource/image-20210724185730868.png)

5. ### **选择默认实例**

   ![image-20210724185849490](../../resource/image-20210724185849490.png)

6. ### [==服务器配置==很重要](https://jingyan.baidu.com/article/f3ad7d0f30cc5e09c3345b86.html)

   ![image-20210724185933468](../../resource/image-20210724185933468.png)

   将数据库引擎设置为SYSTEM

7. ### 设置密码，选择混合模式，添加当前用户，可能会有点慢

   ![image-20210724190217576](../../resource/image-20210724190217576.png)

8. ### 后面的都要添加当前用户，然后一直下一步

   ![image-20210724190254549](../../resource/image-20210724190254549.png)

9. ### 客户端设置

   一个用户名，随便设置

   ![image-20210724190408885](../../resource/image-20210724190408885.png)

10. ### 然后就是一直下一步，最后安装会花费大概五六分钟

    