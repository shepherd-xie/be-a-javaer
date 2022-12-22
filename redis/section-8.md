# Redis 发布/订阅

Redis 可以实现简单的消息订阅及发布，详细内容参考官方文档：[Redis Pub/Sub](https://redis.io/docs/manual/pubsub/)

## 示例

- 消息订阅者

  ```shell
  127.0.0.1:6379> SUBSCRIBE channel
  Reading messages... (press Ctrl-C to quit)
  1) "subscribe"
  2) "channel"
  3) (integer) 1
  1) "message"
  2) "channel"
  3) "hello,world"
  ```

- 消息发布者

```shell
127.0.0.1:6379> PUBLISH channel hello,world
(integer) 1
```

