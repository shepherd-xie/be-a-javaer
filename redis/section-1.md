# Redis 入门

## Redis 的安装

1. 使用 Docker 运行 Redis：`docker run --name redis -d redis`

```shell
➜  redis docker run --name redis -d redis
fd4d81e5b4d509aed545113f525f057dbc66e8ec6403d08144b7212b6f305b31
➜  redis docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS      NAMES
fd4d81e5b4d5   redis     "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   6379/tcp   redis
```

2. 使用 `redis docker exec -i -t redis redis-cli` 连接 Redis

```shell
➜  redis docker exec -i -t redis redis-cli
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set name alpha
OK
127.0.0.1:6379> get name
"alpha"
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379>
```

3. 使用 `shutdown` 关闭 Redis

```shell
127.0.0.1:6379> shutdown
➜  redis docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                     PORTS     NAMES
fd4d81e5b4d5   redis     "docker-entrypoint.s…"   4 minutes ago   Exited (0) 7 seconds ago             redis
```

## redis-benchmark 压力测试工具

redis-benchmark 是官方自带的压力测试工具。

详细使用方式参考 [Redis 官方文档](https://redis.io/docs/management/optimization/benchmarks/)

```shell
root@afae1e815bd6:/# redis-benchmark --help
Usage: redis-benchmark [-h <host>] [-p <port>] [-c <clients>] [-n <requests>] [-k <boolean>]

 -h <hostname>      Server hostname (default 127.0.0.1)
 -p <port>          Server port (default 6379)
 -s <socket>        Server socket (overrides host and port)
 -a <password>      Password for Redis Auth
 --user <username>  Used to send ACL style 'AUTH username pass'. Needs -a.
 -c <clients>       Number of parallel connections (default 50)
 -n <requests>      Total number of requests (default 100000)
 -d <size>          Data size of SET/GET value in bytes (default 3)
 --dbnum <db>       SELECT the specified db number (default 0)
 --threads <num>    Enable multi-thread mode.
 --cluster          Enable cluster mode.
 --enable-tracking  Send CLIENT TRACKING on before starting benchmark.
 -k <boolean>       1=keep alive 0=reconnect (default 1)
 -r <keyspacelen>   Use random keys for SET/GET/INCR, random values for SADD,
                    random members and scores for ZADD.
  Using this option the benchmark will expand the string __rand_int__
  inside an argument with a 12 digits number in the specified range
  from 0 to keyspacelen-1. The substitution changes every time a command
  is executed. Default tests use this to hit random keys in the
  specified range.
 -P <numreq>        Pipeline <numreq> requests. Default 1 (no pipeline).
 -q                 Quiet. Just show query/sec values
 --precision        Number of decimal places to display in latency output (default 0)
 --csv              Output in CSV format
 -l                 Loop. Run the tests forever
 -t <tests>         Only run the comma separated list of tests. The test
                    names are the same as the ones produced as output.
 -I                 Idle mode. Just open N idle connections and wait.
 --tls              Establish a secure TLS connection.
 --sni <host>       Server name indication for TLS.
 --cacert <file>    CA Certificate file to verify with.
 --cacertdir <dir>  Directory where trusted CA certificates are stored.
                    If neither cacert nor cacertdir are specified, the default
                    system-wide trusted root certs configuration will apply.
 --insecure         Allow insecure TLS connection by skipping cert validation.
 --cert <file>      Client certificate to authenticate with.
 --key <file>       Private key file to authenticate with.
 --tls-ciphers <list> Sets the list of prefered ciphers (TLSv1.2 and below)
                    in order of preference from highest to lowest separated by colon (":").
                    See the ciphers(1ssl) manpage for more information about the syntax of this string.
 --tls-ciphersuites <list> Sets the list of prefered ciphersuites (TLSv1.3)
                    in order of preference from highest to lowest separated by colon (":").
                    See the ciphers(1ssl) manpage for more information about the syntax of this string,
                    and specifically for TLSv1.3 ciphersuites.
 --help             Output this help and exit.
 --version          Output version and exit.
```

测试：使用 20 个并发线程发送 100,000 个请求 `redis-benchmark -h localhost -p 6379 -n 100000 -c 20`

测试结果部分截图

```shell
====== SET ======
  100000 requests completed in 1.62 seconds
  20 parallel clients # 并发客户端数量
  3 bytes payload # 每次写入载荷
  keep alive: 1 # 可用服务数量
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": no
  multi-thread: no

Latency by percentile distribution:
0.000% <= 0.055 milliseconds (cumulative count 3)
50.000% <= 0.167 milliseconds (cumulative count 63159)
75.000% <= 0.175 milliseconds (cumulative count 75227)
87.500% <= 0.191 milliseconds (cumulative count 89134)
93.750% <= 0.207 milliseconds (cumulative count 93869)
96.875% <= 0.247 milliseconds (cumulative count 97143)
98.438% <= 0.295 milliseconds (cumulative count 98582)
99.219% <= 0.343 milliseconds (cumulative count 99263)
99.609% <= 0.423 milliseconds (cumulative count 99617)
99.805% <= 0.511 milliseconds (cumulative count 99818)
99.902% <= 0.575 milliseconds (cumulative count 99908)
99.951% <= 0.663 milliseconds (cumulative count 99954)
99.976% <= 0.775 milliseconds (cumulative count 99976)
99.988% <= 2.639 milliseconds (cumulative count 99988)
99.994% <= 2.767 milliseconds (cumulative count 99994)
99.997% <= 2.879 milliseconds (cumulative count 99997)
99.998% <= 2.983 milliseconds (cumulative count 99999)
99.999% <= 2.999 milliseconds (cumulative count 100000)
100.000% <= 2.999 milliseconds (cumulative count 100000)

Cumulative distribution of latencies:
0.433% <= 0.103 milliseconds (cumulative count 433)
93.869% <= 0.207 milliseconds (cumulative count 93869)
98.739% <= 0.303 milliseconds (cumulative count 98739)
99.567% <= 0.407 milliseconds (cumulative count 99567)
99.794% <= 0.503 milliseconds (cumulative count 99794)
99.928% <= 0.607 milliseconds (cumulative count 99928)
99.964% <= 0.703 milliseconds (cumulative count 99964)
99.977% <= 0.807 milliseconds (cumulative count 99977)
99.978% <= 0.903 milliseconds (cumulative count 99978)
99.979% <= 1.303 milliseconds (cumulative count 99979)
99.980% <= 1.407 milliseconds (cumulative count 99980)
99.982% <= 1.807 milliseconds (cumulative count 99982)
100.000% <= 3.103 milliseconds (cumulative count 100000)

Summary:
  throughput summary: 61919.50 requests per second # 每秒处理请求数
  latency summary (msec):
          avg       min       p50       p95       p99       max
        0.168     0.048     0.167     0.223     0.327     2.999
```

## Redis 基础

Redis 默认 16 个数据库，默认使用数据库 0，使用 `SELECT <dbid>` 切换数据库

```
# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16
```

```shell
127.0.0.1:6379> keys * # 查看当前所有 key
1) "myhash"
2) "key:__rand_int__"
3) "counter:__rand_int__"
4) "myzset"
5) "stock_account"
6) "mykey"
7) "userInfoKey"
127.0.0.1:6379> flushdb # 清除当前数据库
OK
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> flushall # 清空全部数据库
OK
```

## Redis 端口号的由来

来自 Redis 作者本人的回答 [Appendix: how to remember the Redis port number](http://oldblog.antirez.com/post/redis-as-LRU-cache.html)

> Today on Twitter I saw a tweet related to the ability to remember the Redis port number. There is a trick, the Redis port number, 6379, is **MERZ** at the phone keyboard.
>
> Is it a coincidence that it sounds not random enough? Actually not ;) I selected 6379 because of MERZ, and not the other way around.

## Redis 是单线程的

Redis 基于内存操作，CPU 并不是 Redis 性能瓶颈，Redis 的性能瓶颈来自于机器的内存和网络带宽。

>  [Redis is single-threaded, then how does it do concurrent I/O?](https://stackoverflow.com/questions/10489298/redis-is-single-threaded-then-how-does-it-do-concurrent-i-o)
>  
> 单线程程序肯定可以通过使用 I/O（解）复用机制和事件循环（Redis 就是这样做的）在 I/O 级别提供并发。
>
> 并行是有代价的：在现代硬件上可以找到多个套接字/多个内核，线程之间的同步非常昂贵。另一方面，像 Redis 这样的高效存储引擎的瓶颈往往是网络，远早于 CPU。因此，隔离事件循环（不需要同步）被视为构建高效、可扩展服务器的良好设计。
>
> Redis 操作是原子的这一事实仅仅是单线程事件循环的结果。有趣的是原子性是免费提供的（它不需要同步）。用户可以利用它来实现乐观锁定和其他模式，而无需支付同步开销。

自 Redis 6 官方开始支持多线程，但是核心的数据访问接口仍然是单线程的，I/O读写的功能将交给其他线程去做。
