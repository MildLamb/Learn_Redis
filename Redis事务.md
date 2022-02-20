# Redis事务
Redis事务本质：一组命令的集合  
Redis单条命令是保证原子性的，但事务不保证原子性  

**redis事务的三大特性：**  
1. 单独隔离操作
```
redis事务中所有的命令都会序列化的，按顺序的执行。
事务在执行的过程中，不会被其他客户端的命令请求打断。
```
2. 没有隔离级别的概念
```
队列中的命令没有提交之前都不会实际的被执行，因为事务提交前任何指令都不会被实际执行，
也就不存在”事务内的查询要看到事务里的更新，在事务外查询不能看到”这个让人万分头痛的问题
```
3. 不保证原子性
```
redis事务在执行过程中如果有一条命令执行失败，那么其后的命令仍然可以执行，不会回滚。
```

## 使用
- 开启事务（multi）
- 命令入队
- 执行事务（exec）
- 取消事务（discard）

```bash
127.0.0.1:6379> multi  # 开启事务
OK
127.0.0.1:6379> set k1 v1  # 任务队列
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec  # 执行事务
1) OK
2) OK
3) "v2"
4) OK 
127.0.0.1:6379> multi  # 开启事务
OK
127.0.0.1:6379> set k1 v1  # 任务队列
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> discard  # 放弃事务，任务队列中的任务都不会执行
OK
127.0.0.1:6379> get k4
(nil)
```

# 异常
- 编译期异常（代码错误，命令错误），事务中所有的命令都不会执行
```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> getset k3
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> set k5 v5
QUEUED
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k1
(nil)
```
- 运行时异常，如果事务队列中存在语法性错误，执行命令的时候，其他命令是可以正常执行的，错误命令抛出异常
```bash
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr k1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec
1) (error) ERR value is not an integer or out of range
2) OK
3) OK
4) "v3"
```
