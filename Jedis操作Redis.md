# Jedis
Jedis是官方推荐的Java连接开发工具，使用Java操作Redis中间件。

# 使用
- 导入依赖
```xml
<dependencies>
    <!-- 导入jedis的包 -->
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.7.0</version>
    </dependency>
    <!-- fastjson -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.78</version>
    </dependency>
</dependencies>
```
- 如果要远程连接redis，需要修改redis的配置文件（远程连接一定要设置一个密码，不然很容易被黑）
  - 设置redis密码，在配置文件中找到 requirepass ,这个后面填的就是你自己写的redis密码
  - 将 bind 127.0.0.1 注释掉，注意上面有一个很像的，不要注释错了
  - 将保护模式关闭 protect-mode 改为 no
  - 防火墙开放6379端口
  - 如果用的是云服务器，需要在云服务器开启端口6379开放
- 修改配置文件后需要重启redis服务，关闭redis服务的命令
```bash
[root@VM-16-14-centos bin]# redis-cli -h 127.0.0.1 -p 6379 -a 你的redis服务的密码 shutdown
```
- 然后重启redis服务

# 常用的API
- String
```java
// 2.操作命令
System.out.println("清空数据:" + jedis.flushDB());
System.out.println("判断某个键是否存在:" + jedis.exists("username"));
System.out.println("新增<'username','kindred'>的键值对" + jedis.set("username","kindred"));
System.out.println("新增<'password','12345678'>的键值对" + jedis.set("password","12345678"));
System.out.println("系统中所有的键如下:");
Set<String> keys = jedis.keys("*");
System.out.println(keys);
System.out.println("删除键password:" + jedis.del("password"));
System.out.println("判断password还是否存在:" + jedis.exists("password"));
System.out.println("查看键username所存储的值的类型:" + jedis.type("username"));
System.out.println("随机返回key空间中的一个:" + jedis.randomKey());
System.out.println("重命名key:" + jedis.rename("username","name"));
System.out.println("用新名字name取值:" + jedis.get("name"));
System.out.println("切换数据库:" + jedis.select(0));
System.out.println("删除当前数据库中所有的key:" + jedis.flushDB());
System.out.println("返回当前数据库中key的个数:" + jedis.dbSize());
System.out.println("删除所有数据库中的所有key:" + jedis.flushAll());
```
- List
- Set
- Hash
- Zset
