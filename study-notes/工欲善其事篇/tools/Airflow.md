## Airflow

> 一个脚本的链式调用，crontab 可以很好地处理定时执行任务的需求，无法处理外部系统依赖
>
> 有向无环图

**下载参考**： https://www.jianshu.com/p/a6a5dee5f20a



docker run -itd -p 8080:8080 -v /home/ymg/software/airflow/data:/usr/local/airflow -v /etc/localtime:/etc/localtime --restart always --privileged=true --name airflow  puckel/docker-airflow



作者：转身丶即天涯
链接：https://www.jianshu.com/p/a6a5dee5f20a
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
