# Redis 的数据类型

> [Introduction to Redis](https://redis.io/docs/about/)
>
> Redis 是一种开源（BSD 许可Introduction to Redis）内存**数据结构存储**，用作数据库、缓存、消息代理和流引擎。Redis 提供[数据结构](https://redis.io/docs/data-types/)，例如 [字符串](https://redis.io/docs/data-types/strings/)、[散列](https://redis.io/docs/data-types/hashes/)、[列表](https://redis.io/docs/data-types/lists/)、[集合](https://redis.io/docs/data-types/lists/)、带范围查询的[排序集合、](https://redis.io/docs/data-types/sorted-sets/)[位图](https://redis.io/docs/data-types/bitmaps/)、[hyperloglogs](https://redis.io/docs/data-types/hyperloglogs/)、[地理空间索引](https://redis.io/docs/data-types/geospatial/)和[流](https://redis.io/docs/data-types/streams/)。Redis 具有内置[复制](https://redis.io/topics/replication)、[Lua 脚本](https://redis.io/commands/eval)、[LRU 逐出](https://redis.io/docs/reference/eviction/)、[事务](https://redis.io/topics/transactions)和不同级别的[磁盘持久性](https://redis.io/topics/persistence)，并通过[Redis Sentinel](https://redis.io/topics/sentinel)和[Redis Cluster](https://redis.io/topics/cluster-tutorial)的自动分区提供高可用性。

```shell
127.0.0.1:6379> set name alpha # 设置值
OK
127.0.0.1:6379> get name # 获取值
"alpha"
127.0.0.1:6379> expire name 10 # 设置超时时间
(integer) 1
127.0.0.1:6379> ttl name # 获取存活时间
(integer) 8
127.0.0.1:6379> set age 18
OK
127.0.0.1:6379> move age 1 # 移动 key 到之情数据库
(integer) 1
127.0.0.1:6379> set name orkva
OK
127.0.0.1:6379> type name # 查看 key 的类型
string
127.0.0.1:6379> del name # 删除 key 值
(integer) 1
127.0.0.1:6379> get name
(nil)
127.0.0.1:6379> exists name # 检查 key 是否存在
(integer) 0
```

[Redis 命令参考](https://redis.io/commands/)

## 基础类型

### String

```shell
127.0.0.1:6379> set key1 hello
OK
127.0.0.1:6379> strlen key1 # 字符串长度
(integer) 5
127.0.0.1:6379> append key1 world # 追加字符串
(integer) 10
127.0.0.1:6379> get key1
"helloworld"
127.0.0.1:6379> get key2
(nil)
127.0.0.1:6379> append key2 world # 追加，如果不存在就设置值
(integer) 5
127.0.0.1:6379> get key2
"world"
```

```shell
127.0.0.1:6379> set count 0
OK
127.0.0.1:6379> type count
string
127.0.0.1:6379> incr count # 自增 步长1
(integer) 1
127.0.0.1:6379> get count
"1"
127.0.0.1:6379> decr count # 自减 步长1
(integer) 0
127.0.0.1:6379> get count
"0"
127.0.0.1:6379> incrby count 5 # 指定步长自增
(integer) 5
127.0.0.1:6379> get count
"5"
127.0.0.1:6379> decrby count 10 # 指定步长自减
(integer) -5
127.0.0.1:6379> get count
"-5"
127.0.0.1:6379> set count2 hello
OK
127.0.0.1:6379> type count2
string
127.0.0.1:6379> incr count2
(error) ERR value is not an integer or out of range
```

```shell
127.0.0.1:6379> set key1 hello,world
OK
127.0.0.1:6379> getrange key1 0 4 # 获取部分字符串
"hello"
127.0.0.1:6379> getrange key1 0 -1
"hello,world"
127.0.0.1:6379> setrange key1 6 redis # 替换字符串
(integer) 11
127.0.0.1:6379> get key1
"hello,redis"
```

```shell
127.0.0.1:6379> setex key3 10 hello # set with expire
OK
127.0.0.1:6379> setnx key4 redis # set if not exists
(integer) 1
127.0.0.1:6379> setnx key4 mysql
(integer) 0
127.0.0.1:6379> get key4
"redis"
```

```shell
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3 # multi set
OK
127.0.0.1:6379> mget k1 k2 k3 # multi get
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v2 k4 v5 # multi setnx
(integer) 0
127.0.0.1:6379> get k4
(nil)
```

```shell
127.0.0.1:6379> getset db redis
(nil)
127.0.0.1:6379> getset db mysql
"redis"
127.0.0.1:6379> get db
"mysql"
```

使用场景

- 基础的数据缓存：如 json 数据缓存
- 计数器
- 自增统计场景：如网站访问量、点击量等

### List

```shell
127.0.0.1:6379> lpush list 1 2 3 # left push
(integer) 3
127.0.0.1:6379> lrange list 0 -1 # list range
1) "3"
2) "2"
3) "1"
127.0.0.1:6379> rpush list 4 # right push
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "2"
3) "1"
4) "4"
127.0.0.1:6379> lpop list # left pop
"3"
127.0.0.1:6379> rpop list # right pop
"4"
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "1"
127.0.0.1:6379> lindex list 0 # list index
"2"
127.0.0.1:6379> llen list
(integer) 2
```

```shell
127.0.0.1:6379> lpush list one two three three four
(integer) 5
127.0.0.1:6379> lrange list 0 -1
1) "four"
2) "three"
3) "three"
4) "two"
5) "one"
127.0.0.1:6379> lrem list 1 two # list remove
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "four"
2) "three"
3) "three"
4) "one"
127.0.0.1:6379> lrem list 1 three
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "four"
2) "three"
3) "one"
127.0.0.1:6379> lpush list one
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "one"
2) "four"
3) "three"
4) "one"
127.0.0.1:6379> lrem list 3 one
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "four"
2) "three"
```

```shell
127.0.0.1:6379> lpush list one two three three four
(integer) 5
127.0.0.1:6379> ltrim list 1 4 # 截取指定区域的元素
OK
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
```

```shell
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
127.0.0.1:6379> rpoplpush list otherlist # rpop and lpush
"one"
127.0.0.1:6379> lrange otherlist 0 -1
1) "one"
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
```

```shell
127.0.0.1:6379> lset list 0 two
(error) ERR no such key
127.0.0.1:6379> lpush list one
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "one"
127.0.0.1:6379> lset list 0 two
OK
127.0.0.1:6379> lrange list 0 -1
1) "two"
127.0.0.1:6379> lset list 1 three
(error) ERR index out of range
127.0.0.1:6379> linsert list before two one
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "one"
2) "two"
127.0.0.1:6379> linsert list after two three
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "one"
2) "two"
3) "three"
```

灵活的使用可以在 Redis 中实现队列（`lpush` `rpop`）或栈（`lpush` `lpop`）。

### Set

```shell
127.0.0.1:6379> sadd myset one two three
(integer) 3
127.0.0.1:6379> smembers myset # 查看所有元素
1) "one"
2) "three"
3) "two"
127.0.0.1:6379> sismember myset three # 判断元素是否在 set 中
(integer) 1
127.0.0.1:6379> sismember myset four
(integer) 0
127.0.0.1:6379> scard myset # set 中的元素个数
(integer) 3
127.0.0.1:6379> srem myset two # 删除元素
(integer) 1
127.0.0.1:6379> smembers myset
1) "one"
2) "three"
127.0.0.1:6379> srandmember myset # 随机获取元素
"six"
127.0.0.1:6379> srandmember myset 2
1) "four"
2) "six"
127.0.0.1:6379> spop myset # 移除元素
"five"
127.0.0.1:6379> spop myset
"three"
127.0.0.1:6379> smove myset otherset six
(integer) 1
127.0.0.1:6379> smembers otherset
1) "six"
```

```shell
127.0.0.1:6379> sadd set1 one two three
(integer) 3
127.0.0.1:6379> sadd set2 three four five
(integer) 3
127.0.0.1:6379> sdiff set1 set2 # 差集
1) "one"
2) "two"
127.0.0.1:6379> sdiff set2 set1
1) "four"
2) "five"
127.0.0.1:6379> sinter set1 set2 # 交集
1) "three"
127.0.0.1:6379> sunion set1 set2 # 并集
1) "three"
2) "two"
3) "one"
4) "four"
5) "five"
```

### Hash

映射形式：key - [field - value]

```shell
127.0.0.1:6379> hset myhash name bob age 18
(integer) 2
127.0.0.1:6379> hget myhash name
"bob"
127.0.0.1:6379> hget myhash age
"18"
127.0.0.1:6379> hgetall myhash
1) "name"
2) "bob"
3) "age"
4) "18"
127.0.0.1:6379> hkeys myhash
1) "name"
2) "age"
127.0.0.1:6379> hvals myhash
1) "bob"
2) "18"
127.0.0.1:6379> hdel myhash name
(integer) 1
127.0.0.1:6379> hlen myhash
(integer) 1
127.0.0.1:6379> hexists myhash name
(integer) 0
127.0.0.1:6379> hincrby myhash age 1
(integer) 19
127.0.0.1:6379> hget myhash age
"19"
127.0.0.1:6379> hsetnx myhash name alpha
(integer) 1
127.0.0.1:6379> hget myhash name
"alpha"
```

String 和 Hash 都可以用来存储对象数据，String 使用 Json 的方式存储，而 Hash 使用键值对形式存储。当数据需要更新时，使用 String 存储的数据需要替换 Json，而使用 Hash 存储的数据只需要更新对应字段。

### ZSet（Sorted Set）

```shell
127.0.0.1:6379> zadd salary 2500 alpha
(integer) 1
127.0.0.1:6379> zadd salary 1500 bob
(integer) 1
127.0.0.1:6379> zadd salary 5500 luci
(integer) 1
127.0.0.1:6379> zrange salary -inf +inf byscore
1) "bob"
2) "alpha"
3) "luci"
127.0.0.1:6379> zrange salary -inf +inf byscore withscores
1) "bob"
2) "1500"
3) "alpha"
4) "2500"
5) "luci"
6) "5500"
127.0.0.1:6379> zrange salary +inf -inf byscore rev withscores
1) "luci"
2) "5500"
3) "alpha"
4) "2500"
5) "bob"
6) "1500"
127.0.0.1:6379> zcount salary 1000 3000
(integer) 2
```

## 特殊类型

### Streams

Streams 是 Redis 5.0 中引入的新数据类型，提供了类似于日志形式的数据存储类型，详细使用可参照 [Redis Streams](https://redis.io/docs/data-types/streams/) 以及 [Streams tutorial](https://redis.io/docs/data-types/streams-tutorial/)。

```shell
127.0.0.1:6379> xadd pc-status:shanghai:1007 * temp_c 76.4 cpu 3.4 mem 20.5
"1671609522068-0"
127.0.0.1:6379> xadd pc-status:shanghai:1007 * temp_c 81.2 cpu 6.2 mem 24.1
"1671609535993-0"
127.0.0.1:6379> xadd pc-status:shanghai:1007 * temp_c 92.6 cpu 34.9 mem 57.3
"1671609567182-0"
127.0.0.1:6379> xrange pc-status:shanghai:1007 1671609522068-0 +
1) 1) "1671609522068-0"
  2) 1) "temp_c"
    2) "76.4"
    3) "cpu"
    4) "3.4"
    5) "mem"
    6) "20.5"
2) 1) "1671609535993-0"
  2) 1) "temp_c"
    2) "81.2"
    3) "cpu"
    4) "6.2"
    5) "mem"
    6) "24.1"
3) 1) "1671609567182-0"
  2) 1) "temp_c"
    2) "92.6"
    3) "cpu"
    4) "34.9"
    5) "mem"
    6) "57.3"
```

### geospatial

geospatial 支持存储地理空间位置，可以用来查找指定半径或者区域范围内的位置。

```shell
127.0.0.1:6379> geoadd locations:ca -122.27652 37.805186 station:1
(integer) 1
127.0.0.1:6379> geoadd locations:ca -122.2674626 37.8062344 station:2
(integer) 1
127.0.0.1:6379> geoadd locations:ca -122.2469854 37.8104049 station:3
(integer) 1
127.0.0.1:6379> geosearch locations:ca fromlonlat -122.2612767 37.7936847 byradius 5 km withdist
1) 1) "station:1"
  2) "1.8523"
2) 1) "station:2"
  2) "1.4979"
3) 1) "station:3"
  2) "2.2441"
127.0.0.1:6379> geopos locations:ca station:2
1) 1) "-122.2674599289894104"
  2) "37.80623423353753054"
```

### HyperLogLog

HyperLogLog 是一种估计集合基数的数据结构。作为一种概率数据结构，HyperLogLog 以完美的准确性换取高效的空间利用。

Redis HyperLogLog 实现最多使用 12 KB，并提供 0.81% 的标准错误。

```shell
127.0.0.1:6379> pfadd members 123 456 112 567 433 456
(integer) 1
127.0.0.1:6379> pfcount members
(integer) 5
```

> HyperLogLog 底层结构及其原理
>
> [Redis new data structure: the HyperLogLog](http://antirez.com/news/75)

### bitmaps

Redis 位图是字符串数据类型的扩展，可让您将字符串视为位向量。您还可以对一个或多个字符串执行按位运算。位图用例的一些示例包括：

- 集合成员对应于整数 0-N 的情况的有效集合表示。
- 对象权限，其中每一位代表一个特定的权限，类似于文件系统存储权限的方式。

假设我们有 0-999 个微服务需要按时向服务器 43 发送 heartbeat ping 

```shell
127.0.0.1:6379> setbit server:43:2024-01-01:00-00 123 1
(integer) 0
127.0.0.1:6379> setbit server:43:2024-01-01:00-00 456 1
(integer) 0
127.0.0.1:6379> getbit server:43:2024-01-01:00-00 123
(integer) 1
127.0.0.1:6379> getbit server:43:2024-01-01:00-00 234
(integer) 0
```

