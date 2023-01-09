# JUC 简介

> [Overview (Java Platform SE 8)](https://docs.oracle.com/javase/8/docs/api/)

## 什么是 JUC

JUC 是 JavaSE 提供的一组用于多线程操作的工具类。

- [java.util.concurrent](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html): Utility classes commonly useful in concurrent programming.
- [java.util.concurrent.atomic](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/package-summary.html): A small toolkit of classes that support lock-free thread-safe programming on single variables.
- [java.util.concurrent.locks](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/package-summary.html): Interfaces and classes providing a framework for locking and waiting for conditions that is distinct from built-in synchronization and monitors.

## 并发和并行

- **并发（Concurrent），在操作系统中，是指一个时间段中有几个程序都处于已启动运行到运行完毕之间，且这几个程序都是在同一个处理机上运行。**
- **并行（Parallel），当系统有一个以上CPU时，当一个CPU执行一个进程时，另一个CPU可以执行另一个进程，两个进程互不抢占CPU资源，可以同时进行，这种方式我们称之为并行(Parallel)。**

并发编程的本质：**充分利用 CPU 的资源**

## Java 中线程的状态

- NEW A thread that has not yet started is in this state.
- RUNNABLE A thread executing in the Java virtual machine is in this state.
- BLOCKED A thread that is blocked waiting for a monitor lock is in this state.
- WAITING A thread that is waiting indefinitely for another thread to perform a particular action is in this state.
- TIMED_WAITING A thread that is waiting for another thread to perform an action for up to a specified waiting time is in this state.
- TERMINATED A thread that has exited is in this state

### wait / sleep

|          | wait         | sleep    |
| -------- | ------------ | -------- |
| 来源     | Object       | Thread   |
| 锁       | 释放锁       | 不会释放 |
| 使用场景 | 同步代码块中 | 任何地方 |

