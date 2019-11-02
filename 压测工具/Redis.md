## redis-benchmark

### Redis压力压测

使用自带的压力压测工具

![1567067705235](C:\Users\AlmostLover\Desktop\MarkDown笔记\压测工具\1567067705235.png)

![1567067705235](C:\Users\AlmostLover\Desktop\MarkDown笔记\压测工具\707331-20180201145503750-901697180.png)

```shell
C:\Users\AlmostLover\Desktop\学习资料\Redis-x64-3.2.100>redis-benchmark -h 127.0.0.1 -p 6380 -c 50 -n 10000 -t set
====== SET ======
  10000 requests completed in 0.21 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

75.63% <= 1 milliseconds
99.32% <= 2 milliseconds
99.98% <= 3 milliseconds
100.00% <= 3 milliseconds
46948.36 requests per second
```

