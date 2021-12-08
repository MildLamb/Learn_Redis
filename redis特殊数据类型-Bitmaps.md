# Bitmaps 位存储
应用场景：统计用户信息，登录/未登录 ，签到打卡，等等只有两个状态的都可以考虑Bitmaps  
Bitmaps位图，数据结构，都是操作二进制位来记录，就只有0和1两个状态  
## 使用
使用bitmaps记录一周的签到情况
- setbit：设置bitmaps的某一个点的状态, setbit keyname index status(0 or 1)
- getbit：查看某一个点的状态，getbit keyname index
- bircount：统计一个bitmaps中有多少个1状态，bitcount keyname [startindex endindex]

```bash
127.0.0.1:6379> setbit mybit 0 1
(integer) 0
127.0.0.1:6379> setbit mybit 1 0
(integer) 0
127.0.0.1:6379> setbit mybit 2 1
(integer) 0
127.0.0.1:6379> setbit mybit 3 1
(integer) 0
127.0.0.1:6379> setbit mybit 4 1
(integer) 0
127.0.0.1:6379> setbit mybit 5 1
(integer) 0
127.0.0.1:6379> setbit mybit 6 0
(integer) 0
```
