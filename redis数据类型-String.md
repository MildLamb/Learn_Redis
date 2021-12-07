# 基本数据类型String
- 设置值： set keyname value
- 获取值： get keyname
- 判断是否存在key： exists keyname
- 字符串追加： append keyname addvalue，如果key存在是追加，如果key不存在就相当于set key
- 自增，自减(步长为1)： incr keyname，decr keyname
- 自增，自减(自定义步长)： incrby keyname increment

```bash
127.0.0.1:6379> set name mildlamb
OK
127.0.0.1:6379> get name
"mildlamb"
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> exists name
(integer) 1
127.0.0.1:6379> append name "and wildwolf"
(integer) 20
127.0.0.1:6379> get name
"mildlamband wildwolf"
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views
(integer) 1
127.0.0.1:6379> incr views
(integer) 2
127.0.0.1:6379> incrby views 100
(integer) 102
127.0.0.1:6379> decrby views 50
(integer) 52
```

- 截取字符串： getrange keyname start end
- 替换指定位置的字符串： setrange keyname offset value

```bash
127.0.0.1:6379> set name hello,mildlamb
OK
127.0.0.1:6379> get name
"hello,mildlamb"
127.0.0.1:6379> getrange name 6 -1
"mildlamb"
127.0.0.1:6379> get name
"hello,mildlamb"
127.0.0.1:6379> setrange name 6 wildwolf
(integer) 14
127.0.0.1:6379> get name
"hello,wildwolf"
```

- 设置过期时间(set with expire)： setex
- 不存在则设置(set if not exists)： setnx

```bash
127.0.0.1:6379> setex k3 30 "hello"
OK
127.0.0.1:6379> ttl k3
(integer) 23
127.0.0.1:6379> setnx mykey "redis"  #mykey不存在则创建mykey
(integer) 1
127.0.0.1:6379> keys *
1) "name"
2) "mykey"
127.0.0.1:6379> setnx mykey "mysql"  # mykey存在无法修改
(integer) 0
127.0.0.1:6379> get mykey
"redis"
```

- 批量设置值：mset
- 批量获取值：mget
- 如果不存在则批量设置值：msetnx （这是原子操作，有错误就不会执行）

```bash
127.0.0.1:6379> clear
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k1"
3) "k2"
127.0.0.1:6379> mget k1 k2 k3
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v2 k4 v4
(integer) 0
127.0.0.1:6379> keys *
1) "k3"
2) "k1"
3) "k2"
```

- 对象存储
  - key:json
  - key设置为 user:{id}:{filed} ,直接在键中存放对象想相关属性信息

```bash
127.0.0.1:6379> set user:1 {name:kindred,arg:1500}
OK
127.0.0.1:6379> mset user:1:name mildlamb user:1:age 1500
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "mildlamb"
2) "1500"
```

- 先get再set：getset，先获取值再设置值

```bash
127.0.0.1:6379> getset name mildlamb
(nil)
127.0.0.1:6379> get name
"mildlamb"
127.0.0.1:6379> getset name wildwolf
"mildlamb"
127.0.0.1:6379> get name
"wildwolf"
```
