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
