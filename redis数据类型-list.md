# List
在redis里面，我们可以把list玩成，栈，队列，阻塞队列  
**所有的list命令都是用l/r开头的(头/尾插法)**  
```bash
127.0.0.1:6379> clear
127.0.0.1:6379> LPUSH list one     # 将一个值或者多个值，插入到列表头(左)部
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1   # 获取list中的值
1) "three"
2) "two"
3) "one"


127.0.0.1:6379> RPUSH list2 1       # 将一个值或者多个值，插入到列表尾(右)部
(integer) 1
127.0.0.1:6379> RPUSH list2 2
(integer) 2
127.0.0.1:6379> rpush list2 3
(integer) 3
127.0.0.1:6379> lrange list2 0 -1
1) "1"
2) "2"
3) "3"


127.0.0.1:6379> lpop list    #  移除list的第一个元素
"three"
127.0.0.1:6379> rpop list2   #  移除list2的最后一个元素
"3"
127.0.0.1:6379> lindex list 0   # 获取指定索引的值
"two"
127.0.0.1:6379> lindex list2 0  # 获取指定索引的值
"1"
127.0.0.1:6379> llen list    # 查看list的长度
(integer) 2


127.0.0.1:6379> rpush list kindred gnar neeko
(integer) 3
127.0.0.1:6379> rpush list kindred
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "kindred"
2) "gnar"
3) "neeko"
4) "kindred"
127.0.0.1:6379> lrem list 1 neeko       # list 中移除指定的值  数字代表移除多少个
(integer) 1
127.0.0.1:6379> lrem list 2 kindred
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "gnar"


127.0.0.1:6379> rpush list gnar kindred neeko
(integer) 3
127.0.0.1:6379> ltrim list 1 2        # 通过下标截取指定元素
OK
127.0.0.1:6379> lrange list 0 -1
1) "kindred"
2) "neeko"



127.0.0.1:6379> lrange list 0 -1
1) "gnar"
2) "kindred"
3) "neeko"
127.0.0.1:6379> rpoplpush list hero    # 移除列表的最后一个元素，将他移动到新的列表中
"neeko"
127.0.0.1:6379> lrange list 0 -1
1) "gnar"
2) "kindred"
127.0.0.1:6379> lrange hero 0 -1
1) "neeko"



127.0.0.1:6379> exists list    # 判断是否存在指定键  
(integer) 0
127.0.0.1:6379> lset list 0 kindred   # 不存在列表就去更新就会报错
(error) ERR no such key
127.0.0.1:6379> rpush gnar kindred neeko
(integer) 2
127.0.0.1:6379> lrange gnar 0 0
1) "kindred"
127.0.0.1:6379> lset gnar 0 Kindred    # 通过指定索引替换值
OK
127.0.0.1:6379> lrange gnar 0 -1
1) "Kindred"
2) "neeko"



127.0.0.1:6379> rpush list gnar kindred neeko
(integer) 3
127.0.0.1:6379> linsert list before kindred qsj    # 将某个具体的value插入到指定元素的前面或者后面  before/after
(integer) 4
127.0.0.1:6379> lrange list 0 -1   
1) "gnar"
2) "qsj"
3) "kindred"
4) "neeko"
127.0.0.1:6379> linsert list after kindred test
(integer) 5
```
