# InnoDB 物理结构

## InnoDB索引的物理结构

除空间索引外，`InnoDB` 索引都是[B 树](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_b_tree)数据结构。空间索引使用 [R 树](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_r_tree)，这是用于索引多维数据的专用数据结构。索引记录存储在其 B 树或 R 树数据结构的叶页中。索引页的默认大小为 16KB。[`innodb_page_size`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_page_size)页面大小由MySQL实例初始化时的设置决定 。请参阅 [第 15.8.1 节，“InnoDB 启动配置”](https://dev.mysql.com/doc/refman/8.0/en/innodb-init-startup-configuration.html)。

将新记录插入`InnoDB` [聚簇索引](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_clustered_index)时， `InnoDB`尝试留出 1/16 的页面以供将来插入和更新索引记录。如果按顺序（升序或降序）插入索引记录，则生成的索引页大约为 15/16 满。如果记录以随机顺序插入，则页面从 1/2 到 15/16 满。

`InnoDB`在创建或重建 B 树索引时执行批量加载。这种创建索引的方法称为排序索引构建。该 [`innodb_fill_factor`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_fill_factor)变量定义了在排序索引构建期间填充的每个 B 树页面空间的百分比，剩余空间保留用于未来的索引增长。空间索引不支持排序索引构建。有关详细信息，请参阅 [第 15.6.2.3 节，“排序索引构建”](https://dev.mysql.com/doc/refman/8.0/en/sorted-index-builds.html)。设置 [`innodb_fill_factor`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_fill_factor)为 100 时，聚集索引页中有 1/16 的空间可用于将来的索引增长。

如果索引页面的填充因子`InnoDB`低于`MERGE_THRESHOLD`，如果未指定，默认情况下为 50%，`InnoDB`则尝试收缩索引树以释放页面。该 `MERGE_THRESHOLD`设置适用于 B 树和 R 树索引。有关详细信息，请参阅 [第 15.8.11 节，“为索引页配置合并阈值”](https://dev.mysql.com/doc/refman/8.0/en/index-page-merge-threshold.html)。

## 双写缓冲区

双写缓冲区是一个存储区域，在 `InnoDB`将页面写入 `InnoDB`数据文件中的适当位置之前，写入从缓冲池中刷新的页面。 如果在页面写入过程中 出现操作系统、存储子系统或意外的[**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html)`InnoDB`进程退出，则可以在崩溃恢复期间从双写缓冲区中找到一份完好的页面副本。

虽然数据被写入两次，但双写缓冲区不需要两倍的 I/O 开销或两倍的 I/O 操作。数据以大的顺序块写入双写缓冲区，只需调用一次`fsync()`操作系统（ `innodb_flush_method`设置为 的 情况除外`O_DIRECT_NO_FSYNC`）。