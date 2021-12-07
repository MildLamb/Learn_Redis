# RedisKey基本命令
- 查看所有的key： keys *
- 是否存在key： exists keyname，返回1表示有，返回0表示没有
- 移动指定key： move key dbid
- 设定key的到期时间：expire keyname second
- 查看key的到期时间：ttl keyname
- 获取key对应的value： get keyname
- 查看key对应的类型： type keyname
- 删除一个key： del keyname

```bash
127.0.0.1:6379> set name mildlamb
OK
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> set age 1500
OK
127.0.0.1:6379> keys *
1) "age"
2) "name"
127.0.0.1:6379> exists name
(integer) 1
127.0.0.1:6379> exists asd
(integer) 0
127.0.0.1:6379> move age 1
(integer) 1
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
1) "age"
127.0.0.1:6379[1]> expire name 10
(integer) 0
127.0.0.1:6379[1]> select 0
OK
127.0.0.1:6379> expire name 10
(integer) 1
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> get name
(nil)
127.0.0.1:6379> set name mildlamb
OK
127.0.0.1:6379> expire name 20
(integer) 1
127.0.0.1:6379> ttl name
(integer) 16
127.0.0.1:6379> set name mildlamb
OK
127.0.0.1:6379> type name
string
```
