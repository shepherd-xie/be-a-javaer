# InnoDB 简介

`InnoDB` 是一个兼顾高可靠性和高性能的通用存储引擎。在 MySQL 8.0 中，`InnoDB` 是默认的 MySQL 存储引擎。除非您配置了不同的默认存储引擎，否则在执行 [`CREATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html) 不带`ENGINE` 子句的命令时会创建一个`InnoDB`表。

## InnoDB 的主要优势

- 它的 DML 操作遵循 ACID 模型，事务具有提交、回滚和崩溃恢复功能以保护用户数据。请参阅[第 15.2 节，“InnoDB 和 ACID 模型”](https://dev.mysql.com/doc/refman/8.0/en/mysql-acid.html)。
- 行级锁定和 Oracle 风格的一致性读取提高了多用户并发性和性能。请参阅 [第 15.7 节，“InnoDB 锁定和事务模型”](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-transaction-model.html)。
- `InnoDB`表将您的数据排列在磁盘上以优化基于主键的查询。每个 `InnoDB`表都有一个称为聚簇索引的主键索引，它组织数据以最小化主键查找的 I/O。请参阅[第 15.6.2.1 节，“聚簇索引和二级索引”](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html)。
- 为了保持数据完整性，`InnoDB`支持 `FOREIGN KEY`约束。对于外键，检查插入、更新和删除以确保它们不会导致相关表之间的不一致。请参阅 [第 13.1.20.5 节，“外键约束”](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)。

## 使用 InnoDB 表的好处

- 如果服务器由于硬件或软件问题意外退出，无论当时数据库中发生了什么，您都不需要在重新启动数据库后执行任何特殊操作。`InnoDB`崩溃恢复会自动完成崩溃之前提交的更改，并撤消正在进行但未提交的更改，从而允许您重新启动并从中断的地方继续。请参阅 [第 15.18.2 节，“InnoDB 恢复”](https://dev.mysql.com/doc/refman/8.0/en/innodb-recovery.html)。
- 存储`InnoDB`引擎维护自己的缓冲池，在访问数据时在主内存中缓存表和索引数据。经常使用的数据直接从内存中处理。此缓存适用于多种类型的信息并加快处理速度。在专用数据库服务器上，通常会将高达 80% 的物理内存分配给缓冲池。请参阅[第 15.5.1 节，“缓冲池”](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html)。
- 如果将相关数据拆分到不同的表中，则可以设置外键来强制执行参照完整性。请参阅 [第 13.1.20.5 节，“外键约束”](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)。
- 如果数据在磁盘或内存中损坏，校验和机制会在您使用伪造数据之前提醒您。该 [`innodb_checksum_algorithm`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_checksum_algorithm) 变量定义 所使用的校验和算法 `InnoDB`。
- 当您为每个表设计一个具有适当主键列的数据库时，涉及这些列的操作会自动优化。[`WHERE`](https://dev.mysql.com/doc/refman/8.0/en/select.html) 在子句、[`ORDER BY`](https://dev.mysql.com/doc/refman/8.0/en/select.html)子句、 [`GROUP BY`](https://dev.mysql.com/doc/refman/8.0/en/select.html) 子句和连接操作中引用主键列非常快 。请参阅 [第 15.6.2.1 节，“聚簇索引和二级索引”](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html)。
- 插入、更新和删除通过称为更改缓冲的自动机制进行优化。`InnoDB` 不仅允许对同一个表进行并发读写访问，它还缓存更改的数据以简化磁盘 I/O。请参阅 [第 15.5.2 节，“更改缓冲区”](https://dev.mysql.com/doc/refman/8.0/en/innodb-change-buffer.html)。
- 性能优势不仅限于具有长时间运行查询的大型表。当从表中反复访问相同的行时，自适应哈希索引会接管这些查找，使这些查找更快，就像它们来自哈希表一样。请参阅[第 15.5.3 节，“自适应哈希索引”](https://dev.mysql.com/doc/refman/8.0/en/innodb-adaptive-hash.html)。
- 您可以压缩表和关联的索引。请参阅 [第 15.9 节，“InnoDB 表和页面压缩”](https://dev.mysql.com/doc/refman/8.0/en/innodb-compression.html)。
- 您可以加密您的数据。请参阅 [第 15.13 节，“InnoDB 静态数据加密”](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html)。
- 您可以创建和删除索引以及执行其他 DDL 操作，而对性能和可用性的影响要小得多。请参阅 [第 15.12.1 节，“在线 DDL 操作”](https://dev.mysql.com/doc/refman/8.0/en/innodb-online-ddl-operations.html)。
- 截断 file-per-table 表空间非常快，可以释放磁盘空间供操作系统重用，而不仅仅是`InnoDB`. 请参阅 [第 15.6.3.2 节，“File-Per-Table 表空间”](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html)。
- [`BLOB`](https://dev.mysql.com/doc/refman/8.0/en/blob.html)表数据的存储布局对于长文本字段 更有效 ，采用`DYNAMIC`行格式。请参阅 [第 15.10 节，“InnoDB 行格式”](https://dev.mysql.com/doc/refman/8.0/en/innodb-row-format.html)。
- 您可以通过查询表来监控存储引擎的内部工作`INFORMATION_SCHEMA`。请参阅 [第 15.15 节，“InnoDB INFORMATION_SCHEMA 表”](https://dev.mysql.com/doc/refman/8.0/en/innodb-information-schema.html)。
- 您可以通过查询 Performance Schema 表来监控存储引擎的性能细节。请参阅 [第 15.16 节，“InnoDB 与 MySQL Performance Schema 的集成”](https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-schema.html)。
- 您可以将`InnoDB`表与来自其他 MySQL 存储引擎的表混合使用，即使在同一条语句中也是如此。例如，您可以使用连接操作在单个查询中 合并来自表 `InnoDB`和 表的数据。[`MEMORY`](https://dev.mysql.com/doc/refman/8.0/en/memory-storage-engine.html)
- `InnoDB`专为处理大数据量时的 CPU 效率和最大性能而设计。
- `InnoDB`表可以处理大量数据，即使在文件大小限制为 2GB 的操作系统上也是如此。