# InnoDB 锁定

## 共享锁和独占锁

`InnoDB`实现标准的行级锁定，其中有两种类型的锁， [共享 ( `S`) 锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_shared_lock)和[独占 ( `X`) 锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_exclusive_lock)。

- 共享[( `S`) 锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_shared_lock)允许持有锁的事务读取一行。
- 独占[( `X`) 锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_exclusive_lock)允许持有锁的事务更新或删除行。

如果事务在 row(r) 上`T1`持有共享 (S) 锁，则来自某个不同事务的 T2 对 row(r) 锁的请求将按如下方式处理：

- 当 T2 请求 S 锁时可以立即授予，最终 T1 与 T2 同时获得 row(r) 的 S 锁。
- 当 T2 请求 X 锁时不能立即授予。

如果事务在 row 上`T1`持有排他锁（X），则无法立即授予来自某个不同事务对任一类型锁的请求。相反，事务必须等待事务释放其对 row 的锁定。

## 意向锁

`InnoDB`支持*多粒度锁*，允许行锁和表锁并存。例如，诸如 在指定表上[`LOCK TABLES ... WRITE`](https://dev.mysql.com/doc/refman/8.0/en/lock-tables.html)获取排他锁（一个锁）的语句。`X`为了使多粒度级别的锁定变得切实可行，`InnoDB`请使用 [意向锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_intention_lock)。意向锁是表级锁，指示事务稍后对表中的行需要哪种类型的锁（共享或独占）。意向锁有两种类型：

- 意向[共享锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_intention_shared_lock)( `IS`) 表示事务打算在表中的各个行上设置*共享锁。*
- 意向[排他锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_intention_exclusive_lock)( `IX`) 表示事务打算在表中的各个行上设置排他锁。

例如，[`SELECT ... FOR SHARE`](https://dev.mysql.com/doc/refman/8.0/en/select.html)设置一个`IS`锁，并 [`SELECT ... FOR UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/select.html)设置一个`IX`锁。

意向锁定协议如下：

- 在事务可以获取表中行的共享锁之前，它必须首先获取`IS`表上的锁或更强锁。
- 在事务可以获取表中一行的排他锁之前，它必须首先获取`IX` 表上的锁。

下表总结了表级锁类型兼容性。

|      | `X`  | `IX`   | `S`    | `IS`   |
| :--- | :--- | :----- | :----- | :----- |
| `X`  | 冲突 | 冲突   | 冲突   | 冲突   |
| `IX` | 冲突 | 兼容的 | 冲突   | 兼容的 |
| `S`  | 冲突 | 冲突   | 兼容的 | 兼容的 |
| `IS` | 冲突 | 兼容的 | 兼容的 | 兼容的 |

如果请求事务与现有锁兼容，则将锁授予请求事务，但如果它与现有锁冲突，则不会授予该锁。事务等待，直到释放冲突的现有锁。[如果锁定请求与现有锁定冲突并且由于会导致死锁](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_deadlock)而无法授予 ，则会发生错误。

意向锁不会阻塞除全表请求（例如，[`LOCK TABLES ... WRITE`](https://dev.mysql.com/doc/refman/8.0/en/lock-tables.html)）之外的任何内容。意向锁的主要目的是表明有人正在锁定一行，或者将要锁定表中的一行。

[意向锁的事务数据在InnoDB 监视器](https://dev.mysql.com/doc/refman/8.0/en/innodb-standard-monitor.html)[`SHOW ENGINE INNODB STATUS`](https://dev.mysql.com/doc/refman/8.0/en/show-engine.html)输出 中类似于以下内容 ：

```sql
TABLE LOCK table `test`.`t` trx id 10080 lock mode IX
```

## 间隙锁

间隙锁是在索引记录之间的间隙上的锁，或者是在第一条索引记录之前或最后一条索引记录之后的间隙上的锁。例如，`SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;`阻止其他事务将值插入`15`到列中 `t.c1`，无论该列中是否已经存在任何此类值，因为范围内所有现有值之间的间隙都已锁定。

间隙可能跨越单个索引值、多个索引值，甚至是空的。

间隙锁是性能和并发性之间权衡的一部分，并且用于某些事务隔离级别而不是其他事务隔离级别。

对于使用唯一索引锁定行以搜索唯一行的语句，不需要间隙锁定。（这不包括搜索条件只包括多列唯一索引的某些列的情况；在这种情况下，确实会发生间隙锁定。）例如，如果该列具有唯一索引，则以下语句仅使用`id`一个索引记录锁定值为 100 的行`id`，其他会话是否在前面的间隙中插入行并不重要：

```sql
SELECT * FROM child WHERE id = 100;
```

如果`id`未索引或具有非唯一索引，则该语句会锁定前面的间隙。

这里还值得注意的是，不同事务可以在间隙上持有冲突锁。例如，事务 A 可以在一个间隙上持有一个共享间隙锁（间隙 S 锁），而事务 B 在同一间隙上持有一个独占间隙锁（间隙 X 锁）。允许冲突间隙锁的原因是，如果从索引中清除记录，则必须合并不同事务在记录上持有的间隙锁。

间隙锁`InnoDB`是“纯粹抑制性的”，这意味着它们的唯一目的是防止其他事务插入间隙。间隙锁可以共存。一个事务获取的间隙锁不会阻止另一个事务在同一间隙上获取间隙锁。共享和排他间隙锁之间没有区别。它们彼此不冲突，并且它们执行相同的功能。

可以明确禁用间隙锁定。如果您将事务隔离级别更改为 ，则会发生这种情况 [`READ COMMITTED`](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html#isolevel_read-committed)。在这种情况下，间隙锁定在搜索和索引扫描中被禁用，并且仅用于外键约束检查和重复键检查。

使用隔离级别还有其他影响 [`READ COMMITTED`](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html#isolevel_read-committed)。在 MySQL 评估`WHERE`条件后，释放不匹配行的记录锁。对于 `UPDATE`语句，`InnoDB` 进行“半一致”读取，这样它将最新提交的版本返回给 MySQL，以便 MySQL 可以确定该行是否`WHERE` 匹配[`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/update.html).