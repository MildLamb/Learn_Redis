# Redis发布订阅  
- 三个角色
  - 消息发送者
  - 频道
  - 消息订阅者

![image](https://user-images.githubusercontent.com/92672384/145659530-53752318-6600-4c83-b610-364201fe6ab7.png)  


- 基本命令
  - psubscribe [pattern] : 订阅一个或多个符合给定模式的频道
  - pubsub subcommand [argument...]: 查看订阅与发布系统状态
  - publish channel message : 向一个频道发送一个消息
  - punsubscribe [pattern] : 退订所有给定模式的频道
  - subscribe [channel...] : 订阅一个或多个频道的信息
  - unsubscribe [channel...] : 退订给定的频道

## 消息发布者
```bash
127.0.0.1:6379> publish kindred "hello,mildlamb"  # 向频道kindred发送消息
(integer) 1
127.0.0.1:6379> publish kindred "hello,redis"
(integer) 1
```

## 消息订阅者
```bash
127.0.0.1:6379> subscribe kindred   # 订阅一个频道kindred
Reading messages... (press Ctrl-C to quit)   # 消息提供者向频道发送消息后，如果订阅了对应的频道，消息会自动显示出来
1) "subscribe"
2) "kindred"
3) (integer) 1 
1) "message"     # 消息
2) "kindred"     # 来自哪个频道
3) "hello,mildlamb"   # 消息的内容
1) "message"
2) "kindred"
3) "hello,redis"
```
## 使用场景
- 实时聊天系统
- 实时消息系统
- 订阅，关注系统
- 复杂的场景我们可以使用消息中间件做 MQ
