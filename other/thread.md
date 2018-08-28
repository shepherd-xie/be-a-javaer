# Java线程生命周期

[线程](https://zh.wikipedia.org/zh-hans/%E7%BA%BF%E7%A8%8B)是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并行多个线程，每条线程并行执行不同的任务。

在多核或多CPU，或支持Hyper-threading的CPU上使用多线程程序设计的好处是显而易见，即提高了程序的执行吞吐率。在单CPU单核的计算机上，使用多线程技术，也可以把进程中负责I/O处理、人机交互而常被阻塞的部分与密集计算的部分分开来执行，编写专门的workhorse线程执行密集计算，从而提高了程序的执行效率。

## 线程的基本状态

在Thread类中存在着一个内部类Thread.State，它描述着Java中线程的六种状态

* NEW
A thread that has not yet started is in this state.
* RUNNABLE
A thread executing in the Java virtual machine is in this state.
* BLOCKED
A thread that is blocked waiting for a monitor lock is in this state.
* WAITING
A thread that is waiting indefinitely for another thread to perform a particular action is in this state.
* TIMED_WAITING
A thread that is waiting for another thread to perform an action for up to a specified waiting time is in this state.
* TERMINATED
A thread that has exited is in this state.

线程在给定时间点只能处于一种状态。这些状态是虚拟机状态，不反映任何操作系统线程状态。