# InnoDB 内存结构

## Buffer Pool

缓冲池是主内存中的一个区域，在 `InnoDB`访问时缓存表和索引数据。缓冲池允许直接从内存访问频繁使用的数据，从而加快处理速度。在专用服务器上，通常会将高达 80% 的物理内存分配给缓冲池。

为了提高大容量读取操作的效率，缓冲池被分成可能包含多行的页面。为了缓存管理的效率，缓冲池被实现为页面链表；很少使用的数据使用最近最少使用 (LRU) 算法的变体从缓存中老化。

知道如何利用缓冲池将频繁访问的数据保存在内存中是 MySQL 调优的一个重要方面。

#### 缓冲池 LRU 算法

缓冲池使用 LRU 算法的变体作为列表进行管理。当需要空间将新页面添加到缓冲池时，最近最少使用的页面将被逐出，并将新页面添加到列表的中间。这种中点插入策略将列表视为两个子列表：

- 在头部，最近访问的 新（ “年轻” ）页面的子列表
- 在尾部，最近较少访问的旧页面的子列表

![内容在周围的文字中描述。](https://images.orkva.com/images/2023/02/24/innodb-buffer-pool-list.png)



该算法将经常使用的页面保留在新的子列表中。旧的子列表包含不常用的页面；这些页面是[驱逐](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_eviction)的候选者。

默认情况下，算法运行如下：

- 缓冲池的 3/8 专门用于旧子列表。
- 列表的中点是新子列表的尾部与旧子列表的头部相交的边界。
- 当`InnoDB`将页面读入缓冲池时，它最初将其插入到中点（旧子列表的头部）。可以读取页面，因为它是用户启动的操作（例如 SQL 查询）所必需的， [或者](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_read_ahead)是`InnoDB`.
- 访问旧子列表中的页面使其成为 “新”页面，将其移动到新子列表的头部。如果页面是因为用户启动的操作需要它而被读取的，则第一次访问会立即发生并且该页面成为新页面。如果页面是由于预读操作而被读取的，则第一次访问不会立即发生，并且可能在页面被逐出之前根本不会发生。
- 随着数据库的运行，缓冲池中未访问的页面通过向列表的尾部移动而“老化” 。随着其他页面的更新，新旧子列表中的页面都会老化。随着在中点插入页面，旧子列表中的页面也会老化。最终，未使用的页面到达旧子列表的尾部并被逐出。

默认情况下，查询读取的页面会立即移动到新的子列表中，这意味着它们在缓冲池中停留的时间更长。[**例如，为mysqldump**](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)操作或 `SELECT`没有子句的语句 执行的表扫描`WHERE`可以将大量数据带入缓冲池并驱逐等量的旧数据，即使新数据不再使用也是如此。同样，由预读后台线程加载且仅访问一次的页面将移至新列表的头部。这些情况可以将经常使用的页面推送到旧的子列表，在那里它们会被驱逐。有关优化此行为的信息，请参阅 [第 15.8.3.3 节，“使缓冲池扫描抵抗”](https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-midpoint_insertion.html)和 [第 15.8.3.4 节，“配置 InnoDB 缓冲池预取（预读）”](https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-read_ahead.html)。

`InnoDB`Standard Monitor 输出在`BUFFER POOL AND MEMORY`有关缓冲池 LRU 算法操作的部分中包含几个字段。有关详细信息，请参阅[使用 InnoDB 标准监视器监视缓冲池](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html#innodb-buffer-pool-monitoring)。

#### 缓冲池配置

您可以配置缓冲池的各个方面以提高性能。

- 理想情况下，您将缓冲池的大小设置为尽可能大的值，为服务器上的其他进程留出足够的内存来运行而无需过多的分页。缓冲池越大，就越`InnoDB`像内存数据库，从磁盘读取一次数据，然后在后续读取期间从内存访问数据。请参阅 [第 15.8.3.1 节，“配置 InnoDB 缓冲池大小”](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool-resize.html)。
- 在具有足够内存的 64 位系统上，您可以将缓冲池拆分为多个部分，以最大程度地减少并发操作之间对内存结构的争用。有关详细信息，请参阅[第 15.8.3.2 节，“配置多个缓冲池实例”](https://dev.mysql.com/doc/refman/8.0/en/innodb-multiple-buffer-pools.html)。
- 您可以将经常访问的数据保留在内存中，而不管来自将大量不经常访问的数据带入缓冲池的操作的活动突然激增。有关详细信息，请参阅 [第 15.8.3.3 节，“使缓冲池具有抗扫描性”](https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-midpoint_insertion.html)。
- 您可以控制如何以及何时执行预读请求以异步将页面预取到缓冲池中，以应对即将发生的需求。有关详细信息，请参阅 [第 15.8.3.4 节，“配置 InnoDB 缓冲池预取（预读）”](https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-read_ahead.html)。
- 您可以控制何时发生后台刷新以及是否根据工作负载动态调整刷新率。有关详细信息，请参阅 [第 15.8.3.5 节，“配置缓冲池刷新”](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool-flushing.html)。
- 您可以配置如何`InnoDB`保留当前缓冲池状态，以避免在服务器重启后出现冗长的预热期。有关详细信息，请参阅 [第 15.8.3.6 节，“保存和恢复缓冲池状态”](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)。

#### 使用 InnoDB 标准监视器监视缓冲池

`InnoDB`可以使用访问的标准监视器输出 [`SHOW ENGINE INNODB STATUS`](https://dev.mysql.com/doc/refman/8.0/en/innodb-standard-monitor.html)提供有关缓冲池操作的指标。缓冲池指标位于Standard Monitor 输出`BUFFER POOL AND MEMORY`部分 `InnoDB`：

```sql
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 2198863872
Dictionary memory allocated 776332
Buffer pool size   131072
Free buffers       124908
Database pages     5720
Old database pages 2071
Modified db pages  910
Pending reads 0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 4, not young 0
0.10 youngs/s, 0.00 non-youngs/s
Pages read 197, created 5523, written 5060
0.00 reads/s, 190.89 creates/s, 244.94 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not
0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read
ahead 0.00/s
LRU len: 5720, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
```

下表描述了 `InnoDB`标准监视器报告的缓冲池指标。

Standard Monitor 输出中提供的每秒平均值`InnoDB`基于自上次 `InnoDB`打印 Standard Monitor 输出以来经过的时间。

## Change Buffer

更改缓冲区是一种特殊的数据结构， 当这些页面不在 [缓冲池](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_buffer_pool) 中时，它会缓存对[二级索引](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_secondary_index)页面的更改。缓冲的更改可能由[`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html)[`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/update.html)[`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/delete.html)(DML) 引起 ，稍后当其他读取操作将页面加载到缓冲池中时，这些更改将被合并。

![Content is described in the surrounding text.](https://images.orkva.com/images/2023/02/24/innodb-change-buffer.png)

[与聚集索引](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_clustered_index) 不同，二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新可能会影响索引树中不相邻的二级索引页。当其他操作将受影响的页面读入缓冲池时，稍后合并缓存的更改可避免将二级索引页面从磁盘读入缓冲池所需的大量随机访问 I/O。

在系统大部分空闲时或缓慢关闭期间运行的清除操作会定期将更新的索引页写入磁盘。与立即将每个值写入磁盘相比，清除操作可以更有效地为一系列索引值写入磁盘块。

当有许多受影响的行和许多二级索引要更新时，更改缓冲区合并可能需要几个小时。在此期间，磁盘 I/O 增加，这可能会导致磁盘绑定查询显着减慢。更改缓冲区合并也可能在提交事务后继续发生，甚至在服务器关闭并重新启动后（ 有关更多信息， 请参阅[第 15.21.3 节，“强制 InnoDB 恢复” ）。](https://dev.mysql.com/doc/refman/8.0/en/forcing-innodb-recovery.html)

在内存中，变化缓冲区占据缓冲池的一部分。在磁盘上，更改缓冲区是系统表空间的一部分，当数据库服务器关闭时，索引更改将被缓冲。

更改缓冲区中缓存的数据类型由 [`innodb_change_buffering`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_change_buffering)变量控制。有关详细信息，请参阅 [配置更改缓冲](https://dev.mysql.com/doc/refman/8.0/en/innodb-change-buffer.html#innodb-change-buffer-configuration)。您还可以配置最大更改缓冲区大小。有关详细信息，请参阅 [配置更改缓冲区的最大大小](https://dev.mysql.com/doc/refman/8.0/en/innodb-change-buffer.html#innodb-change-buffer-maximum-size)。

如果索引包含降序索引列或主键包含降序索引列，则二级索引不支持更改缓冲。

### Adaptive Hash Index

自适应哈希索引能够`InnoDB`在具有适当的工作负载组合和足够的缓冲池内存的系统上更像内存数据库，而不会牺牲事务功能或可靠性。自适应哈希索引由变量启用 [`innodb_adaptive_hash_index`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_adaptive_hash_index) ，或在服务器启动时由 关闭 `--skip-innodb-adaptive-hash-index`。

基于观察到的搜索模式，使用索引键的前缀构建哈希索引。前缀可以是任意长度，也可能只是B树中的某些值出现在哈希索引中。哈希索引是针对经常访问的索引页面按需构建的。

如果一个表几乎完全适合主内存，则哈希索引通过启用对任何元素的直接查找来加速查询，将索引值转换为一种指针。 `InnoDB`有一个监控索引搜索的机制。如果`InnoDB`注意到查询可以从构建哈希索引中获益，它会自动这样做。

对于某些工作负载，哈希索引查找的加速大大超过了监视索引查找和维护哈希索引结构的额外工作。在繁重的工作负载（例如多个并发连接）下，访问自适应哈希索引有时会成为争用的源头。`LIKE`带有运算符和通配符的查询 `%` 也往往不会受益。对于无法从自适应哈希索引中获益的工作负载，将其关闭可减少不必要的性能开销。由于很难提前预测自适应哈希索引是否适合特定系统和工作负载，因此请考虑在启用和禁用它的情况下运行基准测试。

自适应散列索引功能是分区的。每个索引都绑定到一个特定的分区，每个分区都由一个单独的锁存器保护。分区由变量控制 [`innodb_adaptive_hash_index_parts`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_adaptive_hash_index_parts) 。默认情况下，该 [`innodb_adaptive_hash_index_parts`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_adaptive_hash_index_parts) 变量设置为 8。最大设置为 512。

`SEMAPHORES`您可以在输出部分 监视自适应哈希索引的使用和争用 [`SHOW ENGINE INNODB STATUS`](https://dev.mysql.com/doc/refman/8.0/en/show-engine.html)。如果有大量线程在等待创建的 rw-latches `btr0sea.c`，请考虑增加自适应哈希索引分区的数量或禁用自适应哈希索引。

有关散列索引的性能特征的信息，请参阅[第 8.3.9 节，“B 树和散列索引的比较”](https://dev.mysql.com/doc/refman/8.0/en/index-btree-hash.html)。

## Log Buffer

日志缓冲区是存储要写入磁盘上日志文件的数据的内存区域。日志缓冲区大小由变量定义 [`innodb_log_buffer_size`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_buffer_size)。默认大小为 16MB。日志缓冲区的内容会定期刷新到磁盘。大型日志缓冲区使大型事务无需在事务提交之前将重做日志数据写入磁盘即可运行。因此，如果您有更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘 I/O。

该 [`innodb_flush_log_at_trx_commit`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit) 变量控制日志缓冲区的内容如何写入和刷新到磁盘。该 [`innodb_flush_log_at_timeout`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_flush_log_at_timeout) 变量控制日志刷新频率。

有关相关信息，请参阅 [内存配置](https://dev.mysql.com/doc/refman/8.0/en/innodb-init-startup-configuration.html#innodb-startup-memory-configuration)和 [第 8.5.4 节，“优化 InnoDB 重做日志记录”](https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-logging.html)。