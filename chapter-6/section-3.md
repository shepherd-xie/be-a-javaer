# Data Access/Integration

## JdbcTemplate

* `<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate" p:dataSource-ref="dataSource"/>`

## Transaction

> #### [事务隔离](https://zh.wikipedia.org/wiki/%E4%BA%8B%E5%8B%99%E9%9A%94%E9%9B%A2)
>
> * 一个数据库事务通常包含了一个序列的对数据库的读/写操作。它的存在包含有以下两个目的：
>
>   1.  为数据库操作序列提供了一个从失败中恢复到正常状态的方法，同时提供了数据库即使在异常状态下仍能保持一致性的方法。
>   2.  当多个应用程序在并发访问数据库时，可以在这些应用程序之间提供一个隔离方法，以防止彼此的操作互相干扰。
>
> * 并非任意的对数据库的操作序列都是数据库事务。数据库事务拥有以下四个特性，习惯上被称之为**ACID特性**。
>
>   * **原子性（Atomicity）**：事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行。
>   * **一致性（Consistency）**：事务应确保数据库的状态从一个一致状态转变为另一个一致状态。*一致状态*的含义是数据库中的数据应满足完整性约束。
>   * **隔离性（Isolation）**：多个事务并发执行时，一个事务的执行不应影响其他事务的执行。
>   * **持久性（Durability）**：已被提交的事务对数据库的修改应该永久保存在数据库中。
>
> * **事务隔离**（英语：Transaction Isolation）定义了数据库系统中一个操作的结果在何时以何种方式对其他并发操作可见。隔离是事务*ACID*四大属性之一。
>
> * ANSI/ISO SQL定义的标准隔离级别如下：
>
>   * **可串行化（SERIALIZABLE）**
>
>   最高的隔离级别。
>
>   在基于锁机制并发控制的DBMS实现可串行化，要求在选定对象上的读锁和写锁保持直到事务结束后才能释放。在SELECT的查询中使用一个“WHERE”子句来描述一个范围时应该获得一个“范围锁”（range-locks）。这种机制可以避免“幻影读”（phantom reads）现象（详见下文）。
>
>   当采用不基于锁的[并发控制]时不用获取锁。但当系统探测到几个并发事务有“写冲突”的时候，只有其中一个是允许提交的。这种机制的详细描述见“快照隔离”
>
>   * **可重复读（REPEATABLE READS）**
>
>   在可重复读隔离级别中，基于锁机制并发控制的DBMS需要对选定对象的读锁（read locks）和写锁（write locks）一直保持到事务结束，但不要求“范围锁”，因此可能会发生“幻影读”。
>
>   * **提交读（READ COMMITTED）**
>
>   在提交读级别中，基于锁机制并发控制的DBMS需要对选定对象的写锁一直保持到事务结束，但是读锁在SELECT操作完成后马上释放（因此“不可重复读”现象可能会发生，见下面描述）。和前一种隔离级别一样，也不要求“范围锁”。
>
>   * **未提交读（READ UNCOMMITTED）**
>
>   未提交读是最低的隔离级别。允许“脏读”（dirty reads），事务可以看到其他事务“尚未提交”的修改。
>
>   通过比低一级的隔离级别要求更多的限制，高一级的级别提供更强的隔离性。标准允许事务运行在更强的事务隔离级别上。(如在可重复读隔离级别上执行提交读的事务是没有问题的)
>
> * ***读现象***
>
>   * **脏读**
>
>     当一个事务允许读取另外一个事务修改但未提交的数据时，就可能发生脏读。
>
>     脏读和不可重复读（non-repeatable reads）类似。事务2没有提交造成事务1的语句1两次执行得到不同的结果集。在未提交读隔离级别唯一禁止的是更新混乱，即早期的更新可能出现在后来更新之前的结果集中。
>
>   * **不可重复读**
>     在一次事务中，当一行数据获取两遍得到不同的结果表示发生了“不可重复读”.
>
>     在基于锁的并发控制中“不可重复读”现象发生在当执行SELECT 操作时没有获得读锁或者SELECT操作执行完后马上释放了读锁； 多版本并发控制中当没有要求一个提交冲突的事务回滚也会发生“不可重复读”现象。
>
>   * **幻影读**
>     在事务执行过程中，当两个完全相同的查询语句执行得到不同的结果集。这种现象称为“幻影读（phantom read）”
>
>     当事务没有获取范围锁的情况下执行SELECT ... WHERE操作可能会发生“幻影读”。
>
>     “幻影读”是不可重复读的一种特殊场景：当事务1两次执行SELECT ... WHERE检索一定范围内数据的操作中间，事务2在这个表中创建了(如INSERT)了一行新数据，这条新数据正好满足事务1的“WHERE”子句。
>
> * ***隔离级别vs读现象***
>
>   | 隔离级别 |   脏读   | 不可重复读 |  幻影读  |
>   | :------: | :------: | :--------: | :------: |
>   | 未提交读 | 可能发生 |  可能发生  | 可能发生 |
>   |  提交读  |    -     |  可能发生  | 可能发生 |
>   | 可重复读 |    -     |     -      | 可能发生 |
>   | 可序列化 |    -     |     -      |    -     |
>
>   可序列化（Serializable）隔离级别不等同于可串行化（Serializable）。可串行化调度是避免以上三种现象的必要条件，但不是充分条件。
>   “可能发生”表示这个隔离级别会发生对应的现象，“-”表示不会发生。
>
> * ***隔离级别vs锁持续时间***
>
>   在基于锁的并发控制中，隔离级别决定了锁的持有时间。**"C"**-表示锁会持续到事务提交。 **"S"** –表示锁持续到当前语句执行完毕。如果锁在语句执行完毕就释放则另外一个事务就可以在这个事务提交前修改锁定的数据，从而造成混乱。
>
>   | 隔离级别 | 写操作 | 读操作 | 范围操作 (...where...) |
>   | :------: | :----: | :----: | :--------------------: |
>   | 未提交读 |   S    |   S    |           S            |
>   |  提交读  |   C    |   S    |           S            |
>   | 可重复读 |   C    |   C    |           S            |
>   | 可序列化 |   C    |   C    |           C            |

```java
package org.springframework.transaction;

public interface TransactionDefinition {、
    /**
     * 默认的spring事务传播级别
     * 如果上下文中已经存在事务，那么就加入到事务中执行，如果当前上下文中不存在事务，则新建事务执行。
     */
    int PROPAGATION_REQUIRED = 0;
    /**
     * 如果上下文存在事务，则支持事务加入事务，如果没有事务，则使用非事务的方式执行。
     */
    int PROPAGATION_SUPPORTS = 1;
    /**
     * 要求上下文中必须要存在事务，否则就会抛出异常。
     */
    int PROPAGATION_MANDATORY = 2;
    /**
     * 每次都会新建一个事务，并且同时将上下文中的事务挂起，执行当前新建事务完成以后，上下文事务恢复再执行。
     */
    int PROPAGATION_REQUIRES_NEW = 3;
    /**
     * 不支持事务。上下文中存在事务，则挂起事务，执行当前逻辑，结束后恢复上下文的事务。
     */
    int PROPAGATION_NOT_SUPPORTED = 4;
    /**
     * 要求上下文中不能存在事务，一旦有事务，就抛出异常。
     */
    int PROPAGATION_NEVER = 5;
    /**
     * 如果上下文中存在事务，则嵌套事务执行，如果不存在事务，则新建事务。
     */
    int PROPAGATION_NESTED = 6;

    /**
     * 默认的隔离级别
     * 使用数据库默认的事务隔离级别。
     */
    int ISOLATION_DEFAULT = -1;
    /**
     * 读未提交
     */
    int ISOLATION_READ_UNCOMMITTED = 1;
    /**
     * 读已提交
     */
    int ISOLATION_READ_COMMITTED = 2;
    /**
     * 可重复读
     */
    int ISOLATION_REPEATABLE_READ = 4;
    /**
     * 串行化
     */
    int ISOLATION_SERIALIZABLE = 8;

    /**
     * 默认-1，不超时
     */
    int TIMEOUT_DEFAULT = -1;

    /**
     * 获取事务传播行为
     * @return
     */
    int getPropagationBehavior();
    
    /**
     * 获取事务隔离级别
     * @return
     */
    int getIsolationLevel();

    /**
     * 获取事务超时时间
     * @return
     */
    int getTimeout();

    /**
     * 是否只读事务
     * @return
     */
    boolean isReadOnly();

    String getName();
}
```

* ```xml
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource"/>
  </bean>
  ```

* ```xml
  <tx:advice id="transactionInterceptor" transaction-manager="transactionManager">
      <tx:attributes>
          <tx:method name="transfer" propagation="REQUIRED"/>
      </tx:attributes>
  </tx:advice>
  ```

* ```xml
  <aop:config>
      <aop:pointcut id="transactionPointcut" expression="execution(* com.shieh.service..*.*(..))"/>
      <aop:advisor advice-ref="transactionInterceptor" pointcut-ref="transactionPointcut"/>
  </aop:config>
  ```

* 使用 `<tx:annotation-driven/>` 开启事务注解,使用 `@Transactional` 在业务层声明事务

