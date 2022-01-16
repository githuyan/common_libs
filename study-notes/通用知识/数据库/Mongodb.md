# Mongodb

## 基础操作

### CRUD

1. #### 增加

   ```javascript
   # 添加数据库
   use test #如果test存在，则进入数据库，如果不存在，则创建一个
   
   #在数据库中添加集合（表）
   db.createCollection(name, *options)
   use test 
   db.createCollection(' table ')
   
   # 插入数据
   use test
   db.table.insert( {'aaa':'bbbb'} ) # 插入单条数据
   db.table.insertMany([ {a:'aaa',{b:'bbb'}}])  # 插入多条数据
   db.table.insertOne( {} )
   
   # 将数据编排成一个文件，再传入
   var document = ([{a:'aaa',{b:'bbb'}}]) 
   db.table.insert(document)
   
   ```

2. #### 删除

   ```javascript
   # 删除数据库
   use test 
   db.test.createCollection('table')
   db.dropDatabase()
   
   # 删除数据库中的数据
   db.students.remove( {  } ） 删除本文档全部数据  ( {'name' 'job'} ) 中为删除条件
   
   # 删除集合
   db.table.drop()
   
   # 删除集合下全部文档：
   db.table.deleteMany({})
   
   # 删除 status == A 的全部文档：
   db.table.deleteMany({ status : "A" })
   
   # 删除 status == D 的一个文档：
   db.table.deleteOne( { status: "D" } )
   ```

3. #### 更新

   ```javascript
   db.tables.update(
   	{$and:[{name:'tom'},{age:'34'}]},
   	{$set:{name:'Job'}}, # 键name若存在就更新，若不存在就插入
   	{
   		'upsert':true, # 若不存在这个键，是否插入，默认为false
   		'multi':false, # 是否将找到的记录都更新，默认只更新第一条
   	}
   }
   db.collection.update(
      *query,  # 符合查询条件 query 
      update_object, { a : 'aaa' } # 更新键值对象
      {
        *upsert: <boolean>,  # 如果不存在更新对象，是否插入，默认为false
        *multi: <boolean>,   # 默认为false只更新查找到的第一条记录,为true 则更新多有符合query的记录
        *writeConcern: <document>  # 抛出异常的级别
      }
   )
   ```

4. #### 查询

   ```javascript
   # 查询文档
   show dbs 	# 查询全部数据库
   show tables / show collections	# 查询本数据库全部集合
   db.table.find(*query,*progection)   # 在students文档中的所有数据，(.pretty())以json格式
   db.table.find().pretty()
   ```

### 索引

> #### 索引的优点
>
> **1.减少数据扫描：避免全表扫描代价**
>
> **2.减少内存计算：避免分组排序计算**
>
> **3.提供数据约束：唯一和时间约束性**

1. #### 索引

   > mongodb本身就是键值对格式的，为什么还需要索引？大概是可以将任何一个键设置成索引

   > **知识点一：**
   > **创建索引时,1表示按升序存储,-1表示按降序存储。**

   > **知识点二：**
   > **Mongo提供两种建索引的方式foreground和background。**
   > **前台操作，它会阻塞用户对数据的读写操作直到index构建完毕；**
   > **后台模式，不阻塞数据读写操作，独立的后台线程异步构建索引，此时仍然允许对数据的读写操作。**

   ```js
   db.containers.createIndex({name: 1},{unique:true, background: true})
   // 复合索引
   db.packages.createIndex({ appId: 1, version: 1 },{unique:true, background: true})
   ```

   ```javascript
   // 创建索引,值1表示正序排序，-1表示倒序排序
   　　db.user.createIndex({age:-1})
   
   // 查看userinfos中的所有索引
   　　db.user.getIndexes()
   
   // 删除特定一个索引
   　　db.user.dropIndex({name:1,age:-1})
   // 删除所有的索引(主键索引_id不会被删除)
   　　db.user.dropIndexes()
   
   // 如果我们要修改一个索引的话，可以先删除索引然后在重新添加
   ```

2. #### 索引类型

   ```javascript
   // 单键索引
   db.user.createIndex({'age':-1}) // 逆序
   
   // 对整个文档建立索引，只有在使用文档完整匹配时才会使用到这个索引，例如建立了这样一个索引db.personInfos.createIndex({“address”:1})，那么只有使用db.personInfos.find({“address”:{“province”:”xxx”,”city”:”xxx”,""district":"xxx"}})这种完整匹配时才会使用到这个索引，使用db.personInfos.find({“address.city”:”xxx”})是不会使用到该索引的。
   
   // 查询流程
   // 这条查询语句为再 flight中找到 CA12345，然后再对价格进行排序，需要查询整张表
   db.getCollection('flight').find({flight:“CA12345”}).sort({price: 1})
   
   // 复合索引
   // 直接在创建的时候就对 flight和price 按照升序排序，这样查询的时候只需要查询四条元素就可以了
   db.flights.createIndex({ flight: 1, price: 1 },{background: true})
   
   // 内嵌索引
   // 可以在内层中建立索引
   db.personInfos.createIndex({“address.city”:1})
   ```

### 聚合查询

> db.user.aggregrate({})

1. #### 一些查询方法

   | 表达式    | 描述                                           | 实例                                                         |
   | :-------- | :--------------------------------------------- | :----------------------------------------------------------- |
   | $sum      | 计算总和。                                     | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}]) |
   | $avg      | 计算平均值                                     | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}]) |
   | $min      | 获取集合中所有文档对应值得最小值。             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}]) |
   | $max      | 获取集合中所有文档对应值得最大值。             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}]) |
   | $push     | 在结果文档中插入值到一个数组中。               | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}]) |
   | $addToSet | 在结果文档中插入值到一个数组中，但不创建副本。 | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}]) |
   | $first    | 根据资源文档的排序获取第一个文档数据。         | db.mycol.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}]) |
   | $last     | 根据资源文档的排序获取最后一个文档数据         | db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}]) |

   **关系运算**：大小比较（"$cmp"）、等于（"$eq"）、大于（"$gt"）、大于等于（"$gte"）、小于（"$le"）、小于等于（"$lte"）、不等于（"$ne"）、判断 null （"$ifNull"），这些返回值都是 boolean 值类型的。

   **逻辑运算**：与（"$and"）、或（"$or"）、非 （"$not"）

   **字符串操作**：连接（"$concat"）、截取（"$substr"）、转小写（"$toLower"）

   **算术运算**：加法（**$add**）、减法（**$subtract**）、乘法（**$multipy**）、除法（**$divide**）、求模（**$mod**）

   **类型**

   ```js
   db.col.find({"title" : {$type : 2}})   查询title为String的数据 ，2 代表的就是 String
   ```

   | 类型      | 数字 | 备注 |
   | --------- | ---- | ---- |
   | Double    | 1    |      |
   | String    | 2    |      |
   | Object    | 3    |      |
   | Array     | 4    |      |
   | Boolean   | 8    |      |
   | Date      | 9    |      |
   | Null      | 10   |      |
   | Timestamp | 17   |      |

1. #### 管道

   | 描述符     | 效果                                                         | 备注                   |
   | ---------- | ------------------------------------------------------------ | ---------------------- |
   | $project   | 投影，将数据库中的内容格式化（可计算等）到标准输出           | 可进行各种运算         |
   | $match     | 用于过滤数据，只输出符合条件的文档                           |                        |
   | $skip      | 在聚合管道中跳过指定数量的文档，并返回余下的文档。           | 也可以.skip(2)单独使用 |
   | $limit     | 用来限制MongoDB聚合管道返回的文档数。                        |                        |
   | $sort      | { $sort : {'age':-1}};                                       |                        |
   | $group     | 将 设定_id相同的分为一组，再对每一组进行操作，如使用 {total:$sum:"$price"}统计价格 |                        |
   | $unwind    | 将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值 |                        |
   | $lookup    | 多表的关联，                                                 |                        |
   | $addfields | 添加一个字段                                                 |                        |
   | $geoNear   | 地理位置相关                                                 |                        |

   :dancer: 注意 ，和shell，jquery类似，使用 $ 取字段值

   **project**

   ```js
   // project
   db.sales.aggregate([{ $project : 
       // 默认情况下 _id字段是被包含的，将_id手动设置为0，可以不包含 _id                 
       { _id: 0, item : 1, price: "$price", dateInfo: { day: { $dayOfYear: "$date"}, year: { $year: "$date" } } } 
   }]);
    
   // 返回结果
   { "item" : "abc", "price" : 10, "dateInfo" : { "day" : 60, "year" : 2014 }}
   ```

   **group**

   ```js
   // 虽然使用 $group 操作可以很方便的处理数据，但是需要注意的是，所以的分组数据是无序的，并且都是在内存中操作完成，所有操作的时候，不能支持大数据量。
   
   { "_id" : 1, "item" : "abc", "price" : 10, "quantity" : 2, "date" : ISODate("2014-03-01T08:00:00Z") },
   { "_id" : 2, "item" : "jkl", "price" : 20, "quantity" : 1, "date" : ISODate("2014-03-01T09:00:00Z") },
   
   // 按照 item 字段进行分组，统计每个 item 下面的文档个数
   db.sales.aggregate([
       {
           $group : {
               _id : "$item",   // 按照 $item 的值进行分组，对每一组统计数量
               count: { $sum : 1}
           }
       }
   // 返回结果
   { "_id" : "xyz", "count" : 2 }
   
   // 按照 author 分组，将同一个 author 的title 拼接到一个数组中
   db.books.aggregate(
      [
        { $group : { _id : "$author",
                      books: { $push: "$title" } } } // 这里的顺序为，现将数据按照$author分组，在各组数据中去取出每一条数据的 title 字段，push进books的数组中
      ]
   
   ```

   **lookup**

   ```js
   // $lookup	
   // 多表的关联，就是将第二个表的数据合并为当前表的一个新字段
   $lookup:{
   	{ 	from:table2,
   		localField: origin_field,
   		foreignField: table2_field,  // 不清楚这个连表能不能使用条件
   		as: 'new_field'
   	}
   }
   // 连表条件时 localField == foreignField,将table2集合中符合条件的作为 new_field字段附加到 当前表中
   ```

   **unwind**

   ```js
   // 将数组中的对象抽取出来，扁平化
   db.inventory.insertOne({ "_id" : 1, "item" : "ABC1", sizes: [ "S", "M", "L"] })
   db.inventory.aggregate( [ { $unwind : "$sizes" } ] )
   // 输出结果
   { "_id" : 1, "item" : "ABC1", "sizes" : "S" }
   { "_id" : 1, "item" : "ABC1", "sizes" : "M" }
   { "_id" : 1, "item" : "ABC1", "sizes" : "L" }
   ```


## 应用

### 场景

适合

- 存储大量非结构化和半结构化的数据
- 需要能够迅速水平扩容的数据库
- 高性能的实时数据更新

不适用

- 需要事务控制的场景（最新版已经支持事务）
- 需要使用复杂sql语句的场景





