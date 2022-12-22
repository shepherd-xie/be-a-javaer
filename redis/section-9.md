# Redis 主从复制

> 参考：
>
> - [Redis replication](https://redis.io/docs/management/replication/)
>
> - [Database Master-Slave Replication in the Cloud](https://mariadb.com/ja/resources/blog/database-master-slave-replication-in-the-cloud/)

## 主从复制概念

主从复制可以将数据从一台数据库服务器（主服务器）复制到一台或多台其他数据库服务器（从服务器）。主人记录更新，然后波及到奴隶。从机输出一条消息，表明它已成功接收到更新，从而允许发送后续更新。主从复制可以是同步的也可以是异步的。区别只是变化传播的时间。如果同时对master和slave进行修改，则为同步。如果更改排队并稍后写入，则它是异步的。

主从复制可以实现的功能：

- **备份：** 使用复制作为备份方案，将数据从master复制到slave，然后备份slave的数据。从站可以暂停和关闭而不影响主站的运行操作，因此我们可以生成“实时”数据的有效快照，否则需要关闭主站。
- **向外扩展：** 我们可以使用复制作为横向扩展解决方案；也就是说，我们希望在一些合理的限制范围内将数据库查询的负载分散到多个数据库服务器上。因为复制是从一个主服务器到一个或多个从服务器的分布，所以在我们有大量读取和少量写入/更新的环境中，使用复制进行横向扩展最有效。大多数网站都属于此类，用户在其中浏览网站、阅读文章、帖子或查看产品。更新仅在会话管理期间发生，或者在进行购买或向论坛添加评论/消息时发生。这种情况下的复制使我们能够将读取分布到复制从属服务器上，同时仍然使我们的 Web 服务器能够在需要写入时与复制主服务器通信。
- **分散负载：** 可能会有这样的情况，当我们只有一个主机时，想要将不同的数据库复制到不同的从机。例如，我们可能希望将不同的销售数据分发到不同的部门，以帮助分散数据分析过程中的负载。
- **提高性能：** 随着连接到主站的从站数量的增加，负载虽然很小，但也会增加，因为每个从站都使用客户端连接到主站。此外，由于每个从站都必须接收主站二进制日志的完整副本，主站的网络负载也可能增加并造成瓶颈。如果我们使用连接到一个主服务器的大量从服务器，并且该主服务器也忙于处理请求（例如，作为横向扩展解决方案的一部分），那么我们可能希望提高复制过程的性能。提高复制过程性能的一种方法是创建一个更深层次的复制结构，使 master 能够只复制到一个 slave，并让其余的 slave 连接到这个主要的 slave 以满足它们各自的复制需求。
- **Failover aleviating：** 我们可以设置一个master和一个slave（或者几个slave），然后写一个监控master的脚本，检查它是否up。然后指示我们的应用程序和从服务器在出现故障时更改主服务器。
- **安全性：** 我们可以使用SSL对复制过程中需要的二进制日志的传输进行加密，但是master和slave都必须支持SSL网络连接。如果任一主机不支持 SSL 连接，则无法通过 SSL 连接进行复制。使用 SSL 连接设置复制类似于使用 SSL 设置服务器和客户端。我们必须获得（或创建）一个可以在主服务器上使用的合适的安全证书，并在每个从服务器上获得一个类似的证书（来自相同的证书颁发机构）。

## 集群环境搭建

1. 使用 Docker Compose 搭建 Redis 集群：

```yaml
version: '3'
services:
  redis-alpha:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-alpha
    ports:
      - 6379:6379
    volumes:
      - ./data_alpha:/data
  redis-bravo:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-bravo
    ports:
      - 6380:6379
    volumes:
      - ./data_bravo:/data
  redis-charli:
    image: redis:latest
    restart: unless-stopped
    container_name: redis-charli
    ports:
      - 6381:6379
    volumes:
      - ./data_charli:/data
```

2. 查看当前集群状态

```shell
127.0.0.1:6379> info replication # 查看当前集群状态
# Replication
role:master
connected_slaves:0
master_failover_state:no-failover
master_replid:531f747f2e473336f8d0a52605f0b312be20d7ec
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

3. 从机建立连接

```shell
# docker-compose exec redis-bravo redis-cli
127.0.0.1:6379> SLAVEOF redis-alpha 6379
OK
127.0.0.1:6379> info replication
# Replication
role:slave
master_host:redis-alpha
master_port:6379
master_link_status:up
master_last_io_seconds_ago:6
master_sync_in_progress:0
slave_read_repl_offset:0
slave_repl_offset:0
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:00e1c2bbaa1b45c810255d61e187d2d04bc55224
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:0

# docker-compose exec redis-charli redis-cli
127.0.0.1:6379> SLAVEOF redis-alpha 6379
OK
127.0.0.1:6379> info replication
# Replication
role:slave
master_host:redis-alpha
master_port:6379
master_link_status:up
master_last_io_seconds_ago:4
master_sync_in_progress:0
slave_read_repl_offset:126
slave_repl_offset:126
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:00e1c2bbaa1b45c810255d61e187d2d04bc55224
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:126
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:127
repl_backlog_histlen:0
```

在建立连接后状态会出现 `slave_read_only:1` 表示从机只读。

4. 检查主服务状态

```shell
# docker-compose exec redis-alpha redis-cli
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=172.21.0.2,port=6379,state=online,offset=154,lag=0
slave1:ip=172.21.0.4,port=6379,state=online,offset=154,lag=0
master_failover_state:no-failover
master_replid:00e1c2bbaa1b45c810255d61e187d2d04bc55224
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:154
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:154
```

5. 永久设置请使用配置文件
