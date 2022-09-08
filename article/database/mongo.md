## 1、安装

docker 

拉镜像 docker pull mongo

启动 docker run --name some-mongo -d mongo -p 27017:27017 记得指定端口

## 2、常用命令



db 输出现在的database



use database 切换database



db.collectionName.insertOne(json) 对某个collection插入数据，如果collection不存在，则自动创建

解释

db引用到当前的database

collectionName是指定的collection



```mongo
db.getCollection("testc").find()
```

查询此collection的所有记录



## 3、DDL

创建database

创建collection



## 4、DML

### insert

db.collection.insertOne({})

db.collection.insertMany([{}])



如果不指定 _id 则由mongo生成，具体查看[insert detail](https://docs.mongodb.com/manual/tutorial/insert-documents/#std-label-write-op-insert-behavior)





### query

db.collection.find()  等价于 SELECT * FROM movies



条件

`db.movies.find( { "title": "Titanic" } ) `等价于 `SELECT * FROM movies WHERE title = "Titanic"`



条件操作符

`db.movies.find( { rated: { $in: [ "PG", "PG-13" ] } } )`

等价于

`SELECT * FROM movies WHERE rated in ("PG", "PG-13")`

在mongo中 用in 比用or好



[文档](https://docs.mongodb.com/manual/reference/operator/query/#std-label-query-selectors)



逻辑条件



查询嵌套的对象

```sql
db.test.insertMany([
    { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
    { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
    { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
    { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
    { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
])

db.test.find({size:{h:14,w:21,uom:"cm"}})
db.test.find({"size.h":14})

```







### update



### delete



### mongosh methods





## Aggregation

[doc](https://docs.mongodb.com/manual/aggregation/)



pipeline 管道处理

终结符、中符[doc](https://docs.mongodb.com/manual/reference/operator/aggregation/#std-label-aggregation-expression-operators)





map  reduce



single purpose aggregation method

比如count、distinct





## 索引

[doc](https://docs.mongodb.com/manual/indexes/)

创建索引

