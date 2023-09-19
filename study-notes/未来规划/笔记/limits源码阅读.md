### limits

> 使用各种策略和存储后端（如 redis 和 memcached ）进行速率限制
>
> **github:** https://github.com/alisaifee/limits

#### 整体结构

存储器

策略器

解析器



##### 核心思路

存储唯一标识，开启一个后台线程，在到期后取消这个标识，能够获取到这个表的都在规定时间内，无法获取这个标识则被限流
