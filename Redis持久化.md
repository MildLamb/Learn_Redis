# Redis持久化规则RDB和AOF
- 两种都开启，优先使用AOF恢复数据

# RDB
![image](https://user-images.githubusercontent.com/92672384/145511485-8075bd37-2c96-4cee-adeb-8d100768c715.png)
- rdb保存的文件是 dump.rdb  
触发机制(备份生成dump.rdb文件)：
1. save的规则满足的情况下，会自动触发rdb规则
2. 执行flushall命令，也会触发我们的rdb规则
3. 退出redis服务也会执行save

如何恢复rdb文件  
1. 只需要将rdb文件放在我们的redis启动目录就可以，redis启动的时候会自动检查dump.rdb恢复其中的数据
2. 查看需要存放的位置

优点：  
1. 适合大规模的数据恢复
2. 如果队数据完整性要求不高

缺点：  
1. 需要一定的时间间隔进行操作，如果redis意外宕机，会丢失最后一次修改的数据
2. fork进程的时候，会占用一定的内存空间，fork()用于创建一个子进程，注意是子进程，不是子线程。


# AOF(Append Only File)
- 将我们的所有命令(写操作的命令，读操作不记录)都记录下来，history，恢复的时候就把这个文件再执行一遍  
- aof保存的文件是  appendonly.aof  
- redis.conf 中默认是不开启的，需要手动开启   appendonly yes  
- 如果aof文件有错误，这时候redis是启动不了的，我们需要修复aof文件  
  - redis 给我们提供了一个工具  redis-check-aof
```bash
[root@iZ2zedwea74ejj95zb9sh0Z bin]# redis-check-aof --fix appendonle.aof
```
```bash
[root@VM-16-14-centos bin]# redis-check-aof --fix appendonly.aof 
0x              5e: Expected \r\n, got: 7633
AOF analyzed: size=100, ok_up_to=52, diff=48
This will shrink the AOF from 100 bytes, with 48 bytes, to 52 bytes
Continue? [y/N]: y
Successfully truncated AOF
```

## aof使用
- 配置文件中将 appendonly 改为 yes，重启服务即可

优点：  
1. 每一次修改都同步，文件完整性会更好 # appendfsync always
2. 默认每秒同步一次  # appendfsync everysec
3. 从不同步，效率最高  # appendfsync no

缺点：  
1. 相对于数据rdb来说，aof大小远大于人多不，修复速度也比rdb慢
2. aof运行效率比rdb慢，所以redis默认的配置也是rdb，aof需要去手动开启
