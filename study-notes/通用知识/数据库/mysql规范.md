### mysql一些通用的技巧和规范

##### 建表规范

**参考：**

- [数据库设计-MySQL设计小册](https://juejin.cn/post/7147537194088366093) 

```sql
CREATE TABLE `ytest` (
  `id` int unsigned NOT NULL AUTO_INCREMENT COMMENT '测试',
  `name` varchar(255) COMMENT '测试',
  `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP  COMMENT '更新时间',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT 0 COMMENT '测试', 
  PRIMARY KEY (`id`)
) ENGINE=InnoDB comment "测试demo表";
```



