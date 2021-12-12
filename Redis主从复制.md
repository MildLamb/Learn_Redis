# Redis主从复制
## 概念
主从复制，是将一台redis服务器的数据，复制到其他的redis服务器，前者称为主节点(master/leader)，  
后者称为从节点(slave/follower);数据的复制是单向的，只能由主节点到从节点。Master以写为主，Slave以读为主  

默认情况下，每台redis服务器都是主节点；且一个主节点可以有多个从节点(或没有从节点)，但一个从节点  
只能有一个主节点。  
____
主从复制的作用主要包括：  
1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务(即写redis数据时应用连接主节点，  
读redis数据时应用连接从节点)，分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高redis服务器的  
并发量
4. 高可用基石：除了上述作用外，主从复制还是哨兵和集群能够实现的基础，因此主从复制是redis高可用的基础   
___ 

一般来说，要将redis运用于工程项目中，只使用一台redis是万万不能的，原因是：  
1. 结构上：单个redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大
2. 容量上：单个redis服务器内存容量有限，就算一台redis服务器内容容量为256G，也不能将所有内存用作redis存储内存，一般来说，  
单台redis最大使用内存不应该超过20G
___

查看当前节点信息：info replication  
```bash
127.0.0.1:6379> info replication
# Replication
role:master  # 角色
connected_slaves:0  # 从节点个数
master_replid:0a9051ca213417f200d78772c9ca6c779f126d36
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```


# 环境配置
- 复杂三个配置文件，需要修改一些配置
  - 端口
  - pid文件名称
  - 日志文件名称
  - 持久化文件名称 dumpXxx.rdb
- 通过不同的配置文件启动不同的redis服务(redis-server)
- 默认情况下，启动的redis服务器都是主节点/主机
- 主从配置，我们一般只需要配置从机就好了,配置从机的命令：slaveof host poet
  - 如果主节点的服务器有密码，那么想要称为主节点的从节点需要在配置文件中配置一个属性：masterauth 主节点的密码
- 这是以命令的方式实现主从复制，是暂时的，想要永久生效，就要修改配置文件
  - replicaof <masterip> <masterport>
  - masterauth <master-password>



### 查看从节点
```bash
127.0.0.1:6380> slaveof 127.0.0.1 6379   # 找谁作为主节点，主节点如果有密码，需要在从节点的配置文件中添加 masterauth 主节点密码
OK
127.0.0.1:6380> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:up
master_last_io_seconds_ago:8
master_sync_in_progress:0
slave_repl_offset:0
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:fa1926ab6b39c81e9aaf4b6787d23ec1057349ed
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:0
```
### 查看主节点
```bash
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=127.0.0.1,port=6380,state=online,offset=56,lag=0
slave1:ip=127.0.0.1,port=6381,state=online,offset=56,lag=0
master_replid:fa1926ab6b39c81e9aaf4b6787d23ec1057349ed
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:56
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:56
```

## 细节
- 主机可以写，从机不可以写只能读，主机中的所有信息和数据，都会被从机自动保存
```bash
# 主机
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> set name mildlamb
OK
127.0.0.1:6379> keys *
1) "name"
```
```bash
# 从机
127.0.0.1:6380> keys *
(empty array)
127.0.0.1:6380> keys *
1) "name"
127.0.0.1:6380> set name wildwolf
(error) READONLY You can't write against a read only replica.
```
- 主机中途断开连接，从机依旧是主机的从机，但此时没有写入操作了，这时如果主机回来了，从机依旧可以直接获取到主机写的信息
- 如果从机中途停止服务，如果是命令实现的主从复制，从机重新连接后，又会变成主机，只会保留，断开前从主机获取的数据，但如果重新  
变成主机的从机，就可以获得主机的所有数据了
  
# 复制原理
- Slave启动成功连接到master后，会发送一个sync同步命令
- Master接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕后，master将传送整个数据文件  
到slave，并完成一次完全同步
- 全量复制和增量复制
  - 全量复制：slave服务在接收到数据库文件数据后，将其存盘并加载到内存中
  - 增量复制：Master继续将新的所有收集到的修改命令依次传给slave，完成同步
- 只要是重新连接master，一次完全同步(全量复制)将会自动执行

# 重新变为主节点
```bash
slaveof no one
```
```bash
127.0.0.1:6380> slaveof no  one
OK
127.0.0.1:6380> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=127.0.0.1,port=6381,state=online,offset=115995,lag=0
master_replid:3800c60ba63719f1899ffef56da18f78996b03f0
master_replid2:d8d02d0fdcaac066aacaa74a99e5c0d808b38f15
master_repl_offset:115995
second_repl_offset:115996
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:664
repl_backlog_histlen:115332
```
