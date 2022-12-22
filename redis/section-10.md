# Redis Sentinel

> [High availability with Redis Sentinel](https://redis.io/docs/management/sentinel/)

Redis Sentinel 在不使用[Redis Cluster](https://redis.io/docs/manual/scaling)时为 Redis 提供高可用性。

Redis Sentinel 还提供其他附带任务，例如监控、通知并充当客户端的配置提供程序。

这是宏观层面（即*大图*）的 Sentinel 功能的完整列表：

- **监控**。Sentinel 不断检查您的主实例和副本实例是否按预期工作。
- **通知**。Sentinel 可以通过 API 通知系统管理员或其他计算机程序，其中一个受监控的 Redis 实例出现问题。
- **自动故障转移**。如果 master 没有按预期工作，Sentinel 可以启动一个故障转移过程，其中一个副本被提升为 master，其他额外的副本被重新配置为使用新的 master，并且使用 Redis 服务器的应用程序被告知要使用的新地址连接时。
- **配置提供商**。Sentinel 充当客户端服务发现的权威来源：客户端连接到 Sentinels 以询问负责给定服务的当前 Redis master 的地址。如果发生故障转移，Sentinels 将报告新地址。

## 搭建 Redis Sentinel 集群

1. Redis Sentinel 配置文件

```conf
# sentinel monitor <master-group-name> <ip> <port> <quorum>
sentinel monitor master-alpha redis-alpha 6379 2
```

2. 配置 Docker Compose 文件

```yaml
version: '3'
services:
  redis-alpha:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-alpha
    networks: 
      redis_net:
        ipv4_address: 172.20.0.2
    ports:
      - 6379:6379
    volumes:
      - ./data_alpha:/data
  redis-bravo:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-bravo
    networks: 
      redis_net:
        ipv4_address: 172.20.0.3
    ports:
      - 6380:6379
    volumes:
      - ./data_bravo:/data
  redis-charli:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-charli
    networks: 
      redis_net:
        ipv4_address: 172.20.0.4
    ports:
      - 6381:6379
    volumes:
      - ./data_charli:/data
  redis-sentinel-alpha:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-sentinel-alpha
    networks: 
      redis_net:
        ipv4_address: 172.20.0.5
    ports:
      - 26379:26379
    volumes:
      - ./redis_sentinel_alpha.conf:/sentinel.conf
    command: redis-sentinel /sentinel.conf
  redis-sentinel-bravo:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-sentinel-bravo
    networks: 
      redis_net:
        ipv4_address: 172.20.0.6
    ports:
      - 26380:26379
    volumes:
      - ./redis_sentinel_bravo.conf:/sentinel.conf
    command: redis-sentinel /sentinel.conf
  redis-sentinel-charli:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-sentinel-charli
    networks: 
      redis_net:
        ipv4_address: 172.20.0.7
    ports:
      - 26381:26379
    volumes:
      - ./redis_sentinel_charli.conf:/sentinel.conf
    command: redis-sentinel /sentinel.conf
networks:
  redis_net:
    ipam:
      driver: default
      config:
        - subnet: "172.20.0.0/24"
```

3. 启动后中断 redis-alpha
4. 等待故障恢复

```shell
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:03.107 # +promoted-slave slave 172.20.0.4:6379 172.20.0.4 6379 @ master-alpha 172.20.0.2 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:03.107 # +failover-state-reconf-slaves master master-alpha 172.20.0.2 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:03.111 * +slave-reconf-sent slave 172.20.0.3:6379 172.20.0.3 6379 @ master-alpha 172.20.0.2 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:03.886 # -odown master master-alpha 172.20.0.2 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:04.218 * +slave-reconf-inprog slave 172.20.0.3:6379 172.20.0.3 6379 @ master-alpha 172.20.0.2 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:04.218 * +slave-reconf-done slave 172.20.0.3:6379 172.20.0.3 6379 @ master-alpha 172.20.0.2 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:04.309 # +failover-end master master-alpha 172.20.0.2 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:04.309 # +switch-master master-alpha 172.20.0.2 6379 172.20.0.4 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:04.309 * +slave slave 172.20.0.3:6379 172.20.0.3 6379 @ master-alpha 172.20.0.4 6379
redis-sentinel-bravo   | 1:X 22 Dec 2022 09:57:04.309 * +slave slave 172.20.0.2:6379 172.20.0.2 6379 @ master-alpha 172.20.0.4 6379
```

5. 恢复 redis-alpha

```shell
127.0.0.1:6379> info replication
# Replication
role:slave
master_host:172.20.0.4 # redis-charli
master_port:6379
master_link_status:up
master_last_io_seconds_ago:0
master_sync_in_progress:0
slave_read_repl_offset:57562
slave_repl_offset:57562
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:d07fd0bc536e934a8a7369f8fe2cf320a072f482
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:57562
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:53050
repl_backlog_histlen:4513
```

