# RedisKey基本命令
- 查看所有的key： keys *
- 是否存在key： exists keyname，返回1表示有，返回0表示没有
- 移动指定key： move key dbid
- 设定key的到期时间：expire keyname second
- 查看key的到期时间：ttl keyname
