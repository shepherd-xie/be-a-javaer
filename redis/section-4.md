# Jedis

Jedis is a Java client for [Redis](https://github.com/redis/redis) designed for performance and ease of use.

## Jedis 的基本操作

1. 导入 jedis

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>4.3.0</version>
</dependency>
```

2. 测试建立连接

```java
JedisPool pool = new JedisPool("redis.local", 6379);
try (Jedis jedis = pool.getResource()) {
    System.out.println(jedis.ping());
}
pool.close();
```

常用的 API 和 Redis Client 操作命令完全一样。

## Jedis 的简单事务操作

```java
JedisPool pool = new JedisPool("192.168.80.131", 6379);
try (Jedis jedis = pool.getResource()) {
    Transaction multi = jedis.multi();
    try {
        multi.set("key1", "val1");
        multi.set("key2", "val2");
        int i = 1 / 0;
        multi.exec();
    } catch (Exception e) {
        multi.discard();
    } finally {
        System.out.println(jedis.get("key1"));
        System.out.println(jedis.get("key2"));
    }
}
pool.close();
```

