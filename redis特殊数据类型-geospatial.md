# geospatial 地理位置
只有6个命令
- 添加地理位置：geoadd， geoadd keyname 经度 纬度 位置名称
  - 将指定的地理空间位置（经度、纬度、名称）添加到指定的key中
  - 有效的经度从-180度到180度
  - 有效的纬度从-85.05112878度到85.05112878度
- geohash
- 获取某一个地理位置的经纬度：geopos，geopos keyname 位置名称
- 返回两个位置的直线距离：geodist，geodist keyname 位置1 位置2 单位
  - m 表示单位为米
  - km 表示单位为千米
  - mi 表示单位为英里
  - ft 表示单位为英尺
- georadios
- georadiosbymember

```bash
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijin  # 添加地理位置
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106.50 29.53 chongqin 114.05 22.52 shenzhen 120.16 30.24 hangzhou
(integer) 3
127.0.0.1:6379> geoadd china:city 108.96 34.26 xian
(integer) 1
127.0.0.1:6379> geopos china:city beijin shanghai    # 获取地理位置
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
2) 1) "121.47000163793563843"
   2) "31.22999903975783553"
```
