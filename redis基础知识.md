# Redis基础知识
- redis默认有16个数据库，可以在redis的配置文件中看到
```bash
# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16
```
```bash
127.0.0.1:6379> select 2  # 切换数据库
OK
127.0.0.1:6379[2]> dbsize  # 数据库中有多少记录
(integer) 0
127.0.0.1:6379[2]> set name wildwolf
OK
127.0.0.1:6379[2]> dbsize
(integer) 1
127.0.0.1:6379[2]> keys *  # 查看数据库所有的key
1) "name"
127.0.0.1:6379[2]> flushdb  # 清空当前数据库内容
OK
127.0.0.1:6379[2]> dbsize
(integer) 0
127.0.0.1:6379> flushall  # 清空所有数据库内容
OK
```
