# Zset(有序集合)
在set的基础上，增加了一个值score表示序号
- 添加数据：zadd zsetname score value
- 获取数据：zrange zsetname start end
- 倒叙读取数据：zrevrange zsetname start end
- 根据score将集合中的数据排序展示：zrangebyscore setname -inf +inf [withscores] [LIMIT start end]
- 根据score将集合中的数据倒叙展示：zrevrangebyscore setname +inf -inf [withscores] [LIMIT start end]
  - -inf和+inf 表示选择score的范围，以zrangebyscore为例，是大于等于负无穷，小于等于正无穷，小于小于用(-inf，(+inf表示
- 移除元素：zrem zsetname member
- 查看zset集合中元素个数：zcard zsetname
- 查看score在某个区间内的元素个数：zcount zsetname min max
- 判断两个几个的交集存放到另一个集合中：zinterstore destination numkeys(key的数量) zsetname1 zsetname2
- 判断两个几个的并集存放到另一个集合中：zunionstore destination numkeys(key的数量) zsetname1 zsetname2

```bash
127.0.0.1:6379> zadd myzset 1 kindred  # 添加数据
(integer) 1
127.0.0.1:6379> zadd myzset 2 gnar 3 neeko  # 添加数据
(integer) 2
127.0.0.1:6379> zrange myzset 0 -1
1) "kindred"
2) "gnar"
3) "neeko"
127.0.0.1:6379> zadd salary 2300 mildlamb 1000 wildwolf 100 qsj  
(integer) 3
127.0.0.1:6379> zrangebyscore salary -inf +inf   # 获取数据
1) "qsj"
2) "wildwolf"
3) "mildlamb"
127.0.0.1:6379> keys *
1) "salary"
2) "myzset"
127.0.0.1:6379> zrevrange salary 0 -1
1) "mildlamb"
2) "wildwolf"
3) "qsj"
127.0.0.1:6379> zrevrangebyscore salary +inf -inf withscores  # 根据score倒叙展示数据
1) "mildlamb"
2) "2300"
3) "wildwolf"
4) "1000"
5) "qsj"
6) "100"
127.0.0.1:6379> zrevrangebyscore salary +inf -inf withscores limit 0 2  # 添加额外参数 
1) "mildlamb"
2) "2300"
3) "wildwolf"
4) "1000"
127.0.0.1:6379> zrevrangebyscore salary +inf -inf withscores limit 1 2
1) "wildwolf"
2) "1000"
3) "qsj"
4) "100"
127.0.0.1:6379> zrange salary 0 -1
1) "qsj"
2) "wildwolf"
3) "mildlamb"
127.0.0.1:6379> zrem salary qsj
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "wildwolf"
2) "mildlamb"
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> clear
127.0.0.1:6379> zcard salary # 查看zset集合中元素个数
(integer) 2
127.0.0.1:6379> zadd myzset 5 kindred 10 gnar 15 neeko 20 qsj
(integer) 4
127.0.0.1:6379> zcount myzset (10 (20
(integer) 1
127.0.0.1:6379> zcount myzset 10 20
(integer) 3
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> clear
127.0.0.1:6379> zadd myzset 5 kindred 10 gnar 15 neeko 20 qsj
(integer) 4
127.0.0.1:6379> zcount myzset (10 (20
(integer) 1
127.0.0.1:6379> zcount myzset 10 20
(integer) 3
127.0.0.1:6379> zadd myzset2 5 gnar 10 neeko
(integer) 2
127.0.0.1:6379> zinterstore outset 2 myzset myzset2
(integer) 2
127.0.0.1:6379> zrange outset 0 -1
1) "gnar"
2) "neeko"
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> clear
127.0.0.1:6379> zadd myzset 5 kindred 10 gnar 15 neeko 20 qsj
(integer) 4
127.0.0.1:6379> zadd myzset2 5 mildlamb 10 wildwolf
(integer) 2
127.0.0.1:6379> zunionstore outset 2 myzset myzset2
(integer) 6
127.0.0.1:6379> zrange outset 0 -1
1) "kindred"
2) "mildlamb"
3) "gnar"
4) "wildwolf"
5) "neeko"
6) "qsj"
```
