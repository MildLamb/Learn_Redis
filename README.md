# Learn_Redis
redis中只有网络请求模块和数据操作模块是单线程的。而其他的如持久化存储模块、集群支撑模块等是多线程的

# NoSQL四大分类
- KV键值对
- 文档型数据库
  - MongoDB
  - ConthDB
- 列存储数据库
  - HBase
  - 分布式文件系统
- 图关系数据库
  - Neo4j


### 什么是Redis?
Redis(Remote Dictionary Server),即远程字典服务,是C语言编写开源的，支持网络，可基于内存亦可持久化的日志型，key-value数据库，支持多语言API。

### Redis能干嘛?
1. 内存存储，持久化，内存中是断电即失，所以需要持久化(rdb,aof)
2. 效率高，可以用于高速缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器，计数器
6. ...

### Redis特性
1. 多样的数据类型
2. 持久化
3. 集群
4. 事务

# Redis安装
1. 下载安装包
2. 解压Redis的安装包  建议移动到opt目录下  mv redis-6.0.6.tar.gz /opt
3. 进入解压后的文件，可以看到redis的配置文件
4. 基本环境安装  要进入redis文件中
  - 目前最新版本已经是6.0.8了。,make命令报error，安装Redis6以上需要gcc版本在7以上
  - 运行下面命令升级gcc
  - #第一步
  - sudo yum install centos-release-scl
  - #第二步
  - sudo yum install devtoolset-7-gcc*
  - #第三步
  - scl enable devtoolset-7 bash
```bash
[root@EngulfMissing redis-6.0.6]# yum install gcc-c++

[root@EngulfMissing redis-6.0.6]# make

[root@EngulfMissing redis-6.0.6]# make install
```
5. redis的默认安装路径 /usr/local/bin，redis-cli，redis-server都在这个目录里面
6. 将redis配置文件，复制到我们当前目录下
```bash
[root@EngulfMissing bin]# mkdir rconfig
[root@EngulfMissing bin]# cp /opt/redis-6.0.6/redis.conf rconfig   # 这里的bin是/usr/local/bin
```
7. redis默认不是后台启动的，修改配置文件,将redis.conf中的  daemonize属性改为yes  
如果是云服务器建议为redis设置密码，不然有被黑客拿去挖矿的可能   修改redis.conf里面的requirepass属性，默认是关闭的
```bash
[root@EngulfMissing rconfig]# vim redis.conf
```
8. 启动redis服务，在/usr/local/bin目录下启动redis服务，通过指定的配置文件启动
```bash
[root@EngulfMissing bin]# pwd
/usr/local/bin
[root@EngulfMissing bin]# redis-server myconfig/redis.conf
```
9. 使用redis-cli 连接指定端口号测试，由于设置了密码，还需要密码验证
```bash
[root@VM-16-14-centos bin]# redis-cli -p 6379
127.0.0.1:6379> ping
(error) NOAUTH Authentication required.  
127.0.0.1:6379> auth myredispassword
OK
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set name mildlamb
OK
127.0.0.1:6379> get name
"mildlamb"
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> 
```
10. 查看redis进程是否开启
```bash
[root@EngulfMissing /]# ps -ef|grep redis
```
11. 关闭redis服务
```bash
127.0.0.1:6379> shutdown
not connected> exit
```
