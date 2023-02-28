# InnoDB 和 ACID 模型

[ACID](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_acid) 模型是一组数据库设计原则，强调对业务数据和关键任务应用程序很重要的可靠性方面。MySQL 包括诸如`InnoDB`严格遵守 ACID 模型的存储引擎，因此数据不会损坏，结果也不会因软件崩溃和硬件故障等异常情况而失真。当您依赖 ACID 兼容功能时，您不需要重新发明一致性检查和崩溃恢复机制。如果您有额外的软件保护措施、超可靠的硬件或可以容忍少量数据丢失或不一致的应用程序，您可以调整 MySQL 设置以牺牲一些 ACID 可靠性来换取更高的性能或吞吐量。

以下部分讨论 MySQL 特性，特别是 `InnoDB`存储引擎，如何与 ACID 模型的类别交互：

- **A**：原子性(atomicity)。
- **C**：一致性(consistency)。
- **I**：隔离性(isolation)。
- **D**：耐久性(durability)。

### 原子性

ACID 模型的原子性方面 主要**涉及**`InnoDB` [事务](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_transaction)。相关的 MySQL 特性包括：

- 设置[`autocommit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_autocommit)。
- 声明[`COMMIT`](https://dev.mysql.com/doc/refman/8.0/en/commit.html)。
- 声明[`ROLLBACK`](https://dev.mysql.com/doc/refman/8.0/en/commit.html) 。

### 一致性

ACID 模型的一致性方面主要涉及内部 处理**以**`InnoDB`防止数据崩溃。相关的 MySQL 特性包括：

- 双写缓冲区`InnoDB`。请参见 [第 15.6.4 节，“双写缓冲区”](https://dev.mysql.com/doc/refman/8.0/en/innodb-doublewrite-buffer.html)。
- `InnoDB`崩溃恢复。请参阅 [InnoDB 崩溃恢复](https://dev.mysql.com/doc/refman/8.0/en/innodb-recovery.html#innodb-crash-recovery)。

### 隔离

ACID 模型的隔离方面主要涉及 事务[，](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_transaction)特别是应用于每个事务的**隔离**[级别。](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_isolation_level)相关的 MySQL 特性包括： `InnoDB`

- 设置[`autocommit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_autocommit)。
- 事务隔离级别和[`SET TRANSACTION`](https://dev.mysql.com/doc/refman/8.0/en/set-transaction.html)声明。请参阅 [第 15.7.2.1 节，“事务隔离级别”](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)。
- `InnoDB` [锁定](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_locking) 的底层细节。`INFORMATION_SCHEMA`可以在表（请参阅 [第 15.15.2 节，“InnoDB INFORMATION_SCHEMA 事务和锁定信息”](https://dev.mysql.com/doc/refman/8.0/en/innodb-information-schema-transactions.html)）和性能模式[`data_locks`](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-data-locks-table.html)和 表中查看详细信息[`data_lock_waits`](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-data-lock-waits-table.html)。

### 持久性

ACID 模型的**持久性**方面涉及与特定硬件配置交互的 MySQL 软件功能。由于有多种可能性取决于您的 CPU、网络和存储设备的能力，因此在这方面提供具体指导方针是最复杂的。（这些指导方针可能采取“购买新硬件”的形式 。）相关的 MySQL 功能包括：

- 双写缓冲区`InnoDB`。请参见 [第 15.6.4 节，“双写缓冲区”](https://dev.mysql.com/doc/refman/8.0/en/innodb-doublewrite-buffer.html)。
- 变量 [`innodb_flush_log_at_trx_commit`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit) 。
- 变量[`sync_binlog`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_sync_binlog)。
- 变量[`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_file_per_table) 。
- 存储设备（例如磁盘驱动器、SSD 或 RAID 阵列）中的写入缓冲区。
- 存储设备中的电池供电缓存。
- 用于运行 MySQL 的操作系统，特别是它对`fsync()`系统调用的支持。
- 不间断电源 (UPS) 保护运行 MySQL 服务器和存储 MySQL 数据的所有计算机服务器和存储设备的电力。
- 您的备份策略，例如备份频率和类型，以及备份保留期。
- 对于分布式或托管数据应用程序，MySQL 服务器硬件所在的数据中心的特定特征，以及数据中心之间的网络连接。