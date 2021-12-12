# 哨兵模式
![image](https://user-images.githubusercontent.com/61497283/131593956-889e5e23-aee9-4e12-95ae-ee11a4c68e64.png)  

# 测试（当前模式，一主两从）
- 创建一个配置文件 sentinel.conf,配置如下
```bash
sentinel monitor myredis 127.0.0.1 6379 1  # sentinel monitor 监控名称(随意) 监视的ip 监视的端口 1(数字1表示，监视的端口如果发生了宕机，slave会进行投票选举新的主机)
# 如果你监视的主机有密码，则需要配置， sentinel auth-pass 对应的监视名称 监视的redis服务的密码
```
- 启动哨兵
```bash
[root@VM-16-14-centos bin]# redis-sentinel myconfig/sentinel.conf
```
```bash
4974:X 12 Dec 2021 11:12:16.939 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
4974:X 12 Dec 2021 11:12:16.939 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=4974, just started
4974:X 12 Dec 2021 11:12:16.939 # Configuration loaded
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 6.0.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 4974
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

4974:X 12 Dec 2021 11:12:16.940 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
4974:X 12 Dec 2021 11:12:16.945 # Sentinel ID is da5dd658feb0856ab1bb72fb70af32ec4410b6cf
4974:X 12 Dec 2021 11:12:16.945 # +monitor master myredis 127.0.0.1 6379 quorum 1
4974:X 12 Dec 2021 11:12:46.985 # +sdown master myredis 127.0.0.1 6379
4974:X 12 Dec 2021 11:12:46.985 # +odown master myredis 127.0.0.1 6379 #quorum 1/1
4974:X 12 Dec 2021 11:12:46.985 # +new-epoch 1
4974:X 12 Dec 2021 11:12:46.985 # +try-failover master myredis 127.0.0.1 6379
4974:X 12 Dec 2021 11:12:46.990 # +vote-for-leader da5dd658feb0856ab1bb72fb70af32ec4410b6cf 1
4974:X 12 Dec 2021 11:12:46.990 # +elected-leader master myredis 127.0.0.1 6379
4974:X 12 Dec 2021 11:12:46.990 # +failover-state-select-slave master myredis 127.0.0.1 6379
4974:X 12 Dec 2021 11:12:47.073 # -failover-abort-no-good-slave master myredis 127.0.0.1 6379
4974:X 12 Dec 2021 11:12:47.157 # Next failover delay: I will not start a failover before Sun Dec 12 11:18:47 2021
```
- 如果master节点断开后，监控程序发送心跳包发现master宕机后，就会进行新的选举，我这里选举了6381为新的leader
```bash
7672:X 12 Dec 2021 11:23:29.750 # +try-failover master myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:29.755 # +vote-for-leader da5dd658feb0856ab1bb72fb70af32ec4410b6cf 2
7672:X 12 Dec 2021 11:23:29.755 # +elected-leader master myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:29.755 # +failover-state-select-slave master myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:29.845 # +selected-slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:29.845 * +failover-state-send-slaveof-noone slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:29.928 * +failover-state-wait-promotion slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:30.617 # +promoted-slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:30.617 # +failover-state-reconf-slaves master myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:30.676 * +slave-reconf-sent slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:31.676 * +slave-reconf-inprog slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:31.676 * +slave-reconf-done slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:31.728 # +failover-end master myredis 127.0.0.1 6379
7672:X 12 Dec 2021 11:23:31.728 # +switch-master myredis 127.0.0.1 6379 127.0.0.1 6381
7672:X 12 Dec 2021 11:23:31.729 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6381
7672:X 12 Dec 2021 11:23:31.729 * +slave slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6381
7672:X 12 Dec 2021 11:24:01.807 # +sdown slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6381
```
- 如果原来宕机的主机回来了，那么原来的主机会直接变成新主机的从机，而不是默认为主机

# 哨兵模式优缺点
优点:
1. 哨兵模式，基于主从复制模式，主从的有点，它都有
2. 主从可以切换，故障可以转移，系统的可用性就更好
3. 哨兵模式就是主从模式的升级，从手动到自动，更加健壮

缺点：
1. redis不好在线扩容的，集群容量一旦到达上限，在线扩容十分麻烦
2. 实现真正的哨兵模式配置很复杂
- 哨兵配置
```bash
# Example sentinel.conf

# 哨兵sentinel实例运行的端口 默认26379		如果有哨兵集群，我们还需要配置每个 哨兵端口
port 26379

# 哨兵sentinel的工作目录
dir /tmp

# 哨兵sentinel监控的redis主节点的 ip port
# master-name 可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 2

# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd


# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000

# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
#这个数字越小，完成failover所需的时间就越长，
#但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
#可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1



# 故障转移的超时时间 failover-timeout 可以用在以下这些方面：
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000

# SCRIPTS EXECUTION

#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。

#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
#这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
#一个是事件的类型，
#一个是事件的描述。
#如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
sentinel notification-script mymaster /var/redis/notify.sh

# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh	# 一般都是由运维来配置
```
