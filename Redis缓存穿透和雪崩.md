# 缓存穿透和雪崩
# 缓存穿透（查不到）

![缓存穿透](https://user-images.githubusercontent.com/92672384/145739034-1680ea37-46e5-4aab-86b0-740e14bbb6f6.jpg)

**概述**  
缓存穿透的概念很简单，就是用户想要访问一个数据，发现redis内存数据库(缓存)中没有，于是便向持久层数据库查询。发现也没有，于是本次  
查询失败。当用户量很大的时候，缓存都没有命中，于是大量的请求来到了持久层数据库，就会给持久层数据库造成很大的压力。  

**解决发案**  
- 布隆过滤器
布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免了对底层存储系统的查询压力；  
- 缓存空对象
当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数据将会从缓存中获取，保护后端数据源；  


# 缓存击穿（查的量太大）
**概述**  
与缓存击穿的区别，缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发  
就穿破缓存，就像在一个屏障上凿开一个洞  

当某个key在过期的瞬间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导致  
数据库瞬间压力过大  

**解决发案**  
- 设置热点数据永不过期
从缓存层面来看，没有设置过期时间，所以不会出现热点key过期后产生的问题  
- 加互斥锁
分布式锁：使用分布式锁，保证对于每个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待即可。这种方式将高并发  
的压力转移到了分布式锁，因此对分布式锁的考验很大 

# 缓存雪崩
**概述**  
缓存雪崩，是指在某一个时间段，缓存集中过期失效，Redis宕机!    
**解决发案**  
- Redis高可用
多增设几台Redis，这样一来如果一台Redis服务器挂了，其他的还可以继续工作，就是搭建集群(异地多活)
- 限流降级
在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量，比如对某个key只允许一个线程查询数据和写缓存，其他线程等待
- 数据预热
数据预热的含义就是在正式部署之前，我们先将有可能访问的数据先访问一遍，这样部分可能大量访问的数据就已经先加载到缓存中。在即将发生大并发访问前  
手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间尽量均匀
- 将缓存失效时间分散
我们可以再原有的失效时间基础上增加一个随机值
- 设置过期标志更新缓存
记录缓存数据是否过期(设置提前量)，如果过期会触发通知另外的线程在后台去更新实际key的缓存
