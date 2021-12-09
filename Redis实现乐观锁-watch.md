# 悲观锁和乐观锁
- 悲观锁：认为什么时候都会出问题，无论做什么都会加锁
- 乐观锁：认为什么时候都不会出问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有别人修改过这个数据，version字段，更新的时候先比较version字段

# Redis监视测试
- 正常执行成功
```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money  # 监视money对象
OK
127.0.0.1:6379> multi   # 事务正常结束，在此期间，没有别的线程对数据进行修改
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```
- 在监视期间，被监视的对象在事务执行过程中发生了改变导致事务执行失败
```bash
127.0.0.1:6379> watch money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10
QUEUED
127.0.0.1:6379> incrby out 10
QUEUED
127.0.0.1:6379> exec  # 执行事务前，对money进行了修改
(nil)
127.0.0.1:6379> unwatch  # 想要执行要先取消监视，再重新监视
OK
127.0.0.1:6379> clear
127.0.0.1:6379> watch money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby out 20
QUEUED
127.0.0.1:6379> get money
QUEUED
127.0.0.1:6379> get out
QUEUED
127.0.0.1:6379> exec
1) (integer) 280
2) (integer) 40
3) "280"
4) "40"
```
