# Hash
- 设置hash的值：hset hashname ([fieldname value])...
- 一次设置多个hash值，可以继续使用hset或者使用，hmset：使用方法同上
- 获取hash的值：hget hashname fieldname
- 一次获取多个hash值：hmget hashname fieldname...
- 获取所有的hash值，包括field：hgetall hashname
- 删除指定的hash值：hdel hashname fieldname
- 获取指定hash集合的元素个数：hlen hashname
- 判断hash集合中是否存在指定的field：hexists hashname fieldname
- 获取指定hash集合所有的field：hkeys hashname
- 获取指定hash集合中的所有value：hvals hashname
- 为hash集合中的某个数字字段自增：hincrby hashname fieldname num
- 如果不存在则执行操作：hsetnx hashname (fieldname value)...

```bash
127.0.0.1:6379> hset myhash field1 mildlamb
(integer) 1
127.0.0.1:6379> hset myhash field2 wildwolf field3 gnar # 设置hash的值
(integer) 2
127.0.0.1:6379> hget myhash field1
"mildlamb"
127.0.0.1:6379> hget myhash field1 filed2   # 获取hash的值
(error) ERR wrong number of arguments for 'hget' command
127.0.0.1:6379> hmget myhash field1 field2  #  一次获取多个hash值
1) "mildlamb"
2) "wildwolf"
127.0.0.1:6379> hgetall myhash  # 获取所有的hash值
1) "field1"
2) "mildlamb"
3) "field2"
4) "wildwolf"
5) "field3"
6) "gnar"
127.0.0.1:6379> hdel myhash field3 # 删除指定的hash值
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "field1"
2) "mildlamb"
3) "field2"
4) "wildwolf"
127.0.0.1:6379> hlen myhash  # 获取指定hash集合的元素个数
(integer) 2
127.0.0.1:6379> hexists myhash field3
(integer) 0
127.0.0.1:6379> hexists myhash field1
(integer) 1
127.0.0.1:6379> hkeys myhash  # 获取指定hash集合所有的field
1) "field1"
2) "field2"
127.0.0.1:6379> hvals myhash
1) "mildlamb"
2) "wildwolf"
127.0.0.1:6379> hset myhash field3 5
(integer) 1 
127.0.0.1:6379> hincrby myhash field3 5  # 为hash集合中的某个数字字段自增
(integer) 10 
127.0.0.1:6379> hsetnx myhash field4 test  # 如果不存在则执行操作
(integer) 1
127.0.0.1:6379> hsetnx myhash field4 test  # 如果存在则执行失败
(integer) 0
```

## hash应用
hash可以存放变更的数据，对象，经常变动的信息
