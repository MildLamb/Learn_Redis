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
- redis是单线程的，redis 6版本可以开启多线程了，从测试的消息中可以看到。默认执行仍然是单线程的，官方表示，redis是基于内存操作，CPU不是redis性能瓶颈，redis的瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程了

## Redis为什么单线程还这么快?
误区1：  
高性能的服务器一定是多线程的  
误区2：  
多线程一定比单线程效率高  
核心：  
redis是将多有的数据全部放在内存中的，所以说使用单线程操作就是最高的，多线程之间会有CPU上下文的切换，CPU切换很耗时。对于内存系统来说，没有上下文切换，效率是很高的。  
6.0版本出了，redis不能光回答是单线程，因为其中的文件事件分派器的消费是单线程的，所有redis才叫做单线程模型  
