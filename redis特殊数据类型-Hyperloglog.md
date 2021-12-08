# redis特殊数据类型-Hyperloglog,基数
- 基数是指一个集合中不重复的元素 划重点 是一个集合中不重复的 不是两个集合做对比
- Redis Hyperloglog 基数统计算法

### 优点
占用的内存是固定的，2^64个不同的元素，只需要花费12kb内存；  
比如说网页的UV（unique visitor）  
传统的方式，set保存用户的id，然后统计set的大小，这种方式如果保存大量的用户id就会比较麻烦  
Hyperloglog 有0.81%错误率。

# 使用
- 添加元素：pfadd keyname elements ...
- 判断不重复的元素个数：pfcount keyname
- 合并两个元素集：pfmerge newkey key1 key2 ...

```bash
127.0.0.1:6379> pfadd mykey a b c d e  # 创建第一组元素
(integer) 1
127.0.0.1:6379> pfcount mykey    # 统计第一组元素中的基数
(integer) 5
127.0.0.1:6379> pfadd mykey2 c d e f g h
(integer) 1
127.0.0.1:6379> pfcount mykey2
(integer) 6
127.0.0.1:6379> pfmerge mykey3 mykey mykey2   # 合并两组mykey，mykey2到mykey3(相当于并集)
OK
127.0.0.1:6379> pfcount mykey3
(integer) 8
```
