# geospatial 地理位置
只有6个命令
- geoadd，添加地理位置： geoadd keyname 经度 纬度 位置名称
  - 将指定的地理空间位置（经度、纬度、名称）添加到指定的key中
  - 有效的经度从-180度到180度
  - 有效的纬度从-85.05112878度到85.05112878度
- geohash，将地理位置返回为11个字符的Geohash字符串：geohash keyname 位置名称1 位置名称2 ...
- geopos，获取某一个地理位置的经纬度：geopos keyname 位置名称
- geodist，返回两个位置的直线距离：geodist keyname 位置1 位置2 单位
  - m 表示单位为米
  - km 表示单位为千米
  - mi 表示单位为英里
  - ft 表示单位为英尺
- georadius，以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素：  
  georadius keyname 当前经度 当前纬度 查找半径大小 单位 [withcoord显示坐标] [withdist显示距离] [count 数量]
- georadiusbymember，GEORADIUSBYMEMBER 的中心点是由给定的位置元素决定的

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
127.0.0.1:6379> geodist china:city beijin chongqin km  # 查看两个地理位置的距离
"1464.0708"
127.0.0.1:6379> georadius china:city 110 30 1000 km
1) "chongqin"
2) "xian"
3) "shenzhen"
4) "hangzhou"
127.0.0.1:6379> georadius china:city 110 30 500 km  # 查找存入键中的以指定位置为圆心，半径范围内的地理位置
1) "chongqin"
2) "xian"
127.0.0.1:6379> georadius china:city 110 30 500 km withdist
1) 1) "chongqin"
   2) "341.9374"
2) 1) "xian"
   2) "483.8340"
127.0.0.1:6379> georadius china:city 110 30 500 km withcoord
1) 1) "chongqin"
   2) 1) "106.49999767541885376"
      2) "29.52999957900659211"
2) 1) "xian"
   2) 1) "108.96000176668167114"
      2) "34.25999964418929977"
127.0.0.1:6379> georadius china:city 110 30 500 km count 1
1) "chongqin"
127.0.0.1:6379> georadiusbymember china:city beijin 1000 km  # 和georadius差不多，但中心点是键中的成员
1) "beijin"
2) "xian"
127.0.0.1:6379> geohash china:city  shanghai beijin   # 将地理位置返回为11个字符的Geohash字符串,字符串越像，地理位置就越接近
1) "wtw3sj5zbj0"
2) "wx4fbxxfke0"
```
