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


某CSDN博主的说法：  
```
1、完全基于内存，绝大部分请求是纯粹的内存操作，非常快速。数据存在内存中，
类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)；  

2、数据结构简单，对数据操作也简单，Redis中的数据结构是专门进行设计的；  

3、采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，
不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；  

4、使用多路I/O复用模型，非阻塞IO；  

5、使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样，
Redis直接自己构建了VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求；  

以上几点都比较好理解，下边我们针对多路 I/O 复用模型进行简单的探讨：  

（1）多路 I/O 复用模型  

多路I/O复用模型是利用 select、poll、epoll 可以同时监察多个流的 I/O 事件的能力，在空闲的时候，
会把当前线程阻塞掉，当有一个或多个流有 I/O 事件时，就从阻塞态中唤醒，于是程序就会轮询一遍所有
的流（epoll 是只轮询那些真正发出了事件的流），并且只依次顺序的处理就绪的流，这种做法就避免了大量的无用操作。  

这里“多路”指的是多个网络连接，“复用”指的是复用同一个线程。采用多路 I/O 复用技术可以让单个线程
高效的处理多个连接请求（尽量减少网络 IO 的时间消耗），且 Redis 在内存中操作数据的速度非常快，
也就是说内存内的操作不会成为影响Redis性能的瓶颈，主要由以上几点造就了 Redis 具有很高的吞吐量。  
```
