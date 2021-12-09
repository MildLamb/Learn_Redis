# Spirngboot整合Redis
SpringBoot操作数据：通过spring-data，比如redis mongodb...  
SpringData和SpringBoot是齐名的项目  
在springboot 2.x 之后，原来使用的jedis被替换为了lettuce  
jedis与lettuce的区别：  
- jedis：采用的直连，如果有多个线程操作的话是不安全的，更像BIO
- lettuce：采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况，更像NIO

# 部分源码
```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({RedisOperations.class})
@EnableConfigurationProperties({RedisProperties.class})
@Import({LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class})
public class RedisAutoConfiguration {
    public RedisAutoConfiguration() {
    }

    @Bean
    @ConditionalOnMissingBean(
        name = {"redisTemplate"}
    )  //我们可以自定义一个redisTemplate来替换这个默认的
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //默认的redisTemplate没有过多的设置，redis对象都是需要序列化
        //两个泛型都是Object，后面使用需要强转
        RedisTemplate<Object, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    //由于String是redis中最常用的类型，所以单独提出来了一个bean
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
        return new StringRedisTemplate(redisConnectionFactory);
    }
}
```

# 整合测试
- 导入依赖
```bash
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
- 编写配置
```yml
spring:
  redis:
    host: 150.158.46.233
    port: 6379
    database: 0
    password: mildlambW2kindredwildwolfW2snowgnar
```
- 测试
```java
@SpringBootTest
class Redis02SpringbootApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        // redisTemplate
        // opsForXxx 操作Xxx 例如，opsForValue类似String
        // opsForList
        // opsForSet
        // opsForHash
        // opsForZSet
        // opsForGeo
        // opsForHyperLogLog
        redisTemplate.opsForList().leftPushAll("myfriends","mildlamb","wildwolf","永猎双子-千珏");
        System.out.println(redisTemplate.opsForList().range("myfriends",0,-1));

        //事务
//        redisTemplate.multi();
//        redisTemplate.exec();
//        redisTemplate.discard();

        //获取连接
        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
//        connection.flushDb();
        connection.close();

    }

}
```
- 结果
  - 在java程序中能够正常的设置值和获取值
  - 但通过终端获取redis中的key时，key的前面会有中文乱码，例如key-myfriends变成了->\xac\xed\x00\x05t\x00\tmyfriends

# 自定义RedisTemplate传输User对象
- 新版的springboot好像自动实现了对象的序列化，因此我测试的时候哪怕User对象没有实现Serializable接口依然可以传输成功，但任然还是建议实现Serializable接口
- 
