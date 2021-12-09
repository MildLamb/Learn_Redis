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
- 如果要远程连接redis，需要修改redis的配置文件
  - 将 bind 127.0.0.1 注释掉，注意上面有一个很像的，不要注释错了
  - 将保护模式关闭 protect-mode 改为 no
  - 防火墙开放6379端口
  - 如果用的是云服务器，需要在云服务器开启端口6379开放

# 常用的API
- String
- 
