# ORM

###  ORM的实现原理？

https://blog.csdn.net/qq_41421480/article/details/99407283

ORM的实现基于以下三点

　　映射类：描述数据库表结构，

　　映射文件：指定数据库表和映射类之间的关系

　　数据库配置文件：指定与数据库连接时需要的连接信息(数据库、登录用户名、密码or连接字符串)

```python
# 为表设置引擎
engine =create_engine("mysql+pymysql://root:123456@127.0.0.1:3306/sqldb01?charset=utf8")
    
# 为数据库设置字符编码
__table_args__ = {
    'mysql_charset':'utf8'
    
    # 设置联合唯一索引
     db.UniqueConstraint('user_id', 'post_id', name='uix_user_post_user_id_post_id'),
     db.Index('ix_user_post_user_id_insert_time', 'user_id', 'insert_time'),
}
```



## Turtious

#### 属性

.sql()

> 获取原生sql 
