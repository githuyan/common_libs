### python版grpc框架实践笔记

#### 1 目标与框架需求

1. 灵活的proto文件版本管理



#### 2 实现过程

##### 2.1 protobuf文件版本管理仓库

> 使用git flow灵活管理proto文件版本，灵活升级文件，根据版本号选择文件，每次仓库发生变更都视为一次版本发布，删除所有旧文件，根据proto重新生成python代码

**必要条件：**

- proto文件只增不减
- proto文件的接口只增不减
- proto文件的message字段只增不减
- proot文件中的message字段类型和序号不得修改

