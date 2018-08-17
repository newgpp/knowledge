# MongoDB

## 一、简介

- MongoDB是一个面向文档(document-oriented)的数据库
- 不再有row的概念，而是文档(document)模型
- 不再有预定义模式(predefined schema)
- MongoDB能自动处理跨集群的数据和负载，自动重新分配文档，以及将用户的请求路由到正确的机器上

### 1.1 丰富的功能

- 索引(indexing)
- 聚合(aggragation)
- MongoDB支持存在时间有限的集合

### 1.2 抽象

database collection document

### 1.3 数据类型

- JSON 只支持 null、boolean、数字、字符串、数组、对象
- BSON 支持 null、布尔、数值、字符串、日期、数组、对象、对象id

- MongoDB中存储的文档必须有一个"_id"键。
- 这个键的值可以是任何类型的，默认是一个ObjectId对象。
- 同一个集合中的每个文档都有唯一的"_id"

### 1.4 ObjectId生成规则

The 12-byte ObjectId value consists of:

1. 4-byte value representing the seconds since the Unix epoch,
2. 3-byte machine identifier,
3. 2-byte process id, and
4. 3-byte counter, starting with a random value.

### 1.5 插入校验

- 如果没有"_id"字段就自动增加一个
- 所有文档都必须小于16MB

## 二、常用语句

语句|操作
---|---
db.users.find({"age":27})|查找age为27的文档
db.users.find{"age":{"\$get":18, "\$lte":30}}|查询18~30岁的用户
start=new Date("01/01/2007") db.users.find({"registered":{"$lt":start}})|查找2007-01-01前注册的人
db.users.find({}).sort({"age":-1}).limit(10)|排序取前10位
db.users.find({"username":{"$ne":"joe"}})|不等于joe
db.users.find({}).skip(10).limit(5)|分页
db.users.getIndexes()|查询索引
db.users.ensureIndex({"username":1})|创建索引，3.0.0版本以后更名为createIndex()，ensureIndex()仍可用
db.users.dropIndex({"username":1})|删除索引
db.users.ensureIndex({"username":1, "unique":true})|创建唯一索引
db.users.ensureIndex({"username":1, "background":true})|默认创建索引会阻塞其他数据库操作，{"background":true}在后台创建索引，不阻塞其他数据库操作

## 三、分片

>> https://docs.mongodb.com/manual/sharding/