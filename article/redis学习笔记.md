文档
https://redis.io/documentation

redis是什么？

开源的、内存结构存储，可以用作数据库，缓存，消息代理。



reids提供什么数据结构？

string, hashes, lists, sets, sorted sets

以及暂时不了解的[bitmaps](https://redis.io/topics/data-types-intro#bitmaps), [hyperloglogs](https://redis.io/topics/data-types-intro#hyperloglogs), [geospatial indexes](https://redis.io/commands/geoadd), and [streams](https://redis.io/topics/streams-intro)



还提供什么特性？

Redis has built-in 
- [replication](https://redis.io/topics/replication) 
- [Lua scripting](https://redis.io/commands/eval)
- [LRU eviction](https://redis.io/topics/lru-cache)
- [transactions](https://redis.io/topics/transactions)
-  different levels of [on-disk persistence](https://redis.io/topics/persistence)
-  provides high availability via [Redis Sentinel](https://redis.io/topics/sentinel) 
-  automatic partitioning with [Redis Cluster](https://redis.io/topics/cluster-tutorial).



原子操作有哪些？

like 

- [appending to a string](https://redis.io/commands/append);
- [incrementing the value in a hash](https://redis.io/commands/hincrby); 
- [pushing an element to a list](https://redis.io/commands/lpush); 
- [computing set intersection](https://redis.io/commands/sinter), [union](https://redis.io/commands/sunion) and [difference](https://redis.io/commands/sdiff); 
- or [getting the member with highest ranking in a sorted set](https://redis.io/commands/zrangebyscore).


性能怎么保证？
内存数据集

其他特性

- 事务
- 发布、订阅
- Lua scripting
- keys with a limited time-to-live
- LRU eviction keys
- Automatic failover


实现语言
c

推荐部署
linux

安装
linux

docker
https://hub.docker.com/_/redis

```sh
docker run --name some-redis -d redis
```

连接

```sh
docker ps | findstr "redis-name"
>>> 得到 id

docker exec -it id redis-cli
```



windwos


入门
https://try.redis.io/

- redis 是 key-value存储
- 	set key value
- 		set key value ex n-s
- 	get key
- 	exists key
- 	del key
- 	inc key
- 	incby key number
- 	decr key
- 	decrby key number
- 	expire key s
- 	ttl key  // see left time
- 	

list 
- rpush key value
- 	rpush key value1 value2 value3
- lpush key value
- lrange k start end(-1 mean end of the list)
- lpop k
- rpop k
- llen k


set
- sadd key value
- 	sadd key value1 value2 vlaue3 ...
- 	return 
- 		0 already existed
- 		1 not existed
- srem key value
- sismember key value // determin whther value in the set 
- smembers key  // list all the member of the set
- sunion key1 key2 // union set


hashes
> Hashes are maps between string fields and string values, so they are the perfect data type to represent objects
> 

```sh
    HSET user:1000 name "John Smith"
    HSET user:1000 email "john.smith@example.com"
    HSET user:1000 password "s3cret"
    
    HGETALL user:1000
    >>> name xxx email xxx password xxx
    >>> 
	hget user:1000 name
	>>> value
	>>> 


```
疑问，既然可以存类似对象的结构，那么可以存嵌套的json吗
