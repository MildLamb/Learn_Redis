# Set(集合)，无序不重复的集合
set中的值是不能重复的
- 添加元素：sadd setname member
- 查看元素：smembers setname
- 查看当前集合是否存在元素：sismember setname member
- 查看当前集合中元素的个数：scard setname
- 移除集合中的元素：srem myset member
- 随机删除集合中的元素：spop setname [count]
- 随机抽取指定个数个元素：srandmember setname [count]
- 将指定的值移动到另一个set集合中：smove source destination member

```bash
127.0.0.1:6379> auth mildlambW2kindred
OK
127.0.0.1:6379> sadd myset mildlamb # sadd setname value
(integer) 1
127.0.0.1:6379> sadd myset wildwolf
(integer) 1
127.0.0.1:6379> smembers myset  # smembers setname
1) "wildwolf"
2) "mildlamb"
127.0.0.1:6379> sismember myset mildlamb  # 查看当前集合是否存在元素
(integer) 1
127.0.0.1:6379> sismember myset kindred
(integer) 0
127.0.0.1:6379> scard myset
(integer) 2
127.0.0.1:6379> sadd myset gnar
(integer) 1
127.0.0.1:6379> scard myset
(integer) 3
127.0.0.1:6379> srem myset gnar
(integer) 1
127.0.0.1:6379> smembers myset
1) "wildwolf"
2) "mildlamb"
127.0.0.1:6379> sadd myset gnar neeko qsj
(integer) 3
127.0.0.1:6379> smembers myset
1) "neeko"
2) "wildwolf"
3) "gnar"
4) "mildlamb"
5) "qsj"
127.0.0.1:6379> srandmember myset 2
1) "neeko"
2) "wildwolf"
127.0.0.1:6379> smembers myset
1) "neeko"
2) "wildwolf"
3) "gnar"
4) "mildlamb"
5) "qsj"
127.0.0.1:6379> spop myset
"wildwolf"
127.0.0.1:6379> smembers myset
1) "neeko"
2) "mildlamb"
3) "qsj"
4) "gnar"
127.0.0.1:6379> flushdb
"OK"
127.0.0.1:6379> sadd myset mildlamb wildwolf
(integer) 2
127.0.0.1:6379> sadd myset2 gnar neeko
(integer) 2
127.0.0.1:6379> smove myset myset2 mildlamb
(integer) 1
127.0.0.1:6379> smembers myset
1) "wildwolf"
127.0.0.1:6379> smembers myset2
1) "neeko"
2) "mildlamb"
3) "gnar"
```

- 数学集合操作
  - 交集：sinter setA setB...
  - 并集：sunion setA setB...
  - 差集：sdiff setA setB...

```bash
127.0.0.1:6379> sadd myset a b c d
(integer) 4
127.0.0.1:6379> sadd myset c d e
(integer) 1
127.0.0.1:6379> sadd myset2 d e f
(integer) 3
127.0.0.1:6379> smembers myset
1) "b"
2) "a"
3) "c"
4) "d"
5) "e"
127.0.0.1:6379> smembers myset2
1) "f"
2) "d"
3) "e"
127.0.0.1:6379> sdiff myset myset2
1) "b"
2) "c"
3) "a"
127.0.0.1:6379> sinter myset myset2
1) "d"
2) "e"
127.0.0.1:6379> sunion myset myset2
1) "b"
2) "a"
3) "c"
4) "f"
5) "d"
6) "e"
```
