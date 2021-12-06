# benchmark测试工具
- 官方自带的性能测试工具
- 性能参数如下


| 序号 | 选项 | 描述 | 默认值 |
|:--:|:--:|:--:|:--:|
|1|-h|指定服务器主机ip|127.0.0.1|
|2|-p|指定服务器端口|6379|
|3|-s|指定服务器socket||
|4|-c|指定并发连接数|50|
|5|-n|指定请求数|10000|
|6|-d|以字节的形式指定SET/GET值的数据大小|2|
|7|-k|1=keep alive 0=reconnect|1|
|8|-r|SET/GET/INCR使用随机key SADD 使用随机值||
|9|-P|通过管道传输<numreq>请求|1|
|10|-q|强制退出redis。仅显示query/sec值||
|11|--csv|以CSV格式输出||
|12|-l|生成循环，永久执行测试||
|13|-t|仅运行以逗号分隔的测试命令列表||
  
- 测试
```bash
# 测试40个并发连接，每个连接10000次请求
redis-benchmark -h localhost -p 6379 -c 40 -n 10000
	
...
====== SET ======
  10000 requests completed in 0.16 seconds   # 对我们的1万请求进行写入测试
  40 parallel clients  # 40个并发客户端
  3 bytes payload   # 每次写入3个字节
  keep alive: 1     # 只有一台服务器来处理这些请求
  multi-thread: no

0.01% <= 0.1 milliseconds
0.41% <= 0.2 milliseconds
38.49% <= 0.3 milliseconds
71.06% <= 0.4 milliseconds
83.58% <= 0.5 milliseconds
90.87% <= 0.6 milliseconds
96.48% <= 0.7 milliseconds
98.76% <= 0.8 milliseconds
99.37% <= 0.9 milliseconds
99.79% <= 1.0 milliseconds
99.93% <= 1.1 milliseconds
99.98% <= 1.2 milliseconds
100.00% <= 1.2 milliseconds
63291.14 requests per second
...
```
