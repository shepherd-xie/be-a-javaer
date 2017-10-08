##  Java多线程基础实现

---

### 线程与进程

线程与进程的区别。

Java是一门为数不多的多线程支持的编程语言。

如果要想结束多线程之前首先需要来知道什么叫进程？在操作系统定义中，进程指的是一次程序的完整运行，这个运行的过程之中、内存、处理器、IO等资源都要为这个进程进行服务。

在同一个时间段上，会有多个进程去抢占资源，但是在某一个时间点上，只会有一个进程运行。

线程是在进程基础上进一步的划分结果，即：一个进程上可以同时创建多个线程。

线程是比进程更快的处理单元，而且所占的资源也小。

#### 总结

线程离不开进程。进程如果消失后线程一定会消失，反之如果线程消失了，进程未必会消失。

### 多线程的实现

掌握Java中三种多线程的实现方式（JDK1.5之后增加了第三种）。

如果想要在Java之中是实现多线程有两种途径：

* 继承Thread类；
* 实现Runnable接口（Callable接口）；

### 继承Thread类

Thread类是一个支持多线程的功能类，只要有一个子类它就可以实现多线程的支持。

```java
class MyThread extends Thread { // 这就是一个多线程的操作类
}
```

所有程序的起点是main()方法，但是所有线程也一定要有一个自己的起点，那么这个起点就是run()方法，也就是说在多线程的每个主体类之中都必须覆写Thread类中所提供的run()方法。

```java
public void run() {}
```

这个方法上没有返回值，那么也就表示了线程一旦开始就要一直执行，不能够返回内容。

```java
// 线程操作主类
class MyThread extends Thread { // 这就是一个多线程的操作类
	private String name; // 定义类中的属性
	public MyThread(String name) { // 定义构造方法
		this.name = name;
	}
	@Override
	public void run() { // 覆写run()方法，作为线程的主体操作方法
		for (int i = 0; i < 200; i ++) {
			System.out.println(this.name + " ---> " + i);
		}
	}
}
```

本程序类的功能是进行循环的输出操作，所有的线程与线程是一样的，都必须轮流去抢占资源，所以多线程的执行应该是多个线程彼此交替执行，也就是说如果直接调用了run()方法，并不能启动多线程，多线程启动的唯一方法就是Thread类中的start()方法：public void start()（调用此方法执行的方法体是run()方法定义的）。

```java
public class MainClass { // 主类
	public static void main(String[] args) {
		MyThread mt1 = new MyThread("线程A");
		MyThread mt2 = new MyThread("线程B");
		MyThread mt3 = new MyThread("线程C");
		mt1.start();
		mt2.start();
		mt3.start();
	}
}
```

此时每一个线程对象交替执行。

**疑问？**为什么多线程启动不是调用run()而必须调用start()？

打开Java的源代码，来观察一下start()方法的定义：

```java
/**
 * Causes this thread to begin execution; the Java Virtual Machine
 * calls the <code>run</code> method of this thread.
 * <p>
 * The result is that two threads are running concurrently: the
 * current thread (which returns from the call to the
 * <code>start</code> method) and the other thread (which executes its
 * <code>run</code> method).
 * <p>
 * It is never legal to start a thread more than once.
 * In particular, a thread may not be restarted once it has completed
 * execution.
 *
 * @exception  IllegalThreadStateException  if the thread was already
 *               started.
 * @see        #run()
 * @see        #stop()
 */
public synchronized void start() {
    /**
     * This method is not invoked for the main method thread or "system"
     * group threads created/set up by the VM. Any new functionality added
     * to this method in the future may have to also be added to the VM.
     *
     * A zero status value corresponds to state "NEW".
     */
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    /* Notify the group that this thread is about to be started
     * so that it can be added to the group's list of threads
     * and the group's unstarted count can be decremented. */
    group.add(this);

    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            /* do nothing. If start0 threw a Throwable then
              it will be passed up the call stack */
        }
    }
}

private native void start0();
```

首先方法在Thread类的start()方法里面存在有一个“IllegalThreadStateException”异常抛出。本方法六面使用了throw抛出异常，按照道理来讲应该使用try...catch处理，或者在start()方法声明上使用throws声明，但是此处并没有这样的代码，因为此异常属于RuntimeException的子类，属于选择性处理。如果某一个线程对象重复进行了启动，那么就会抛出此异常。

发现在start()方法里面要调用一的start0()方法，而且此方法的结构与抽象方法类似，唯一不同的是使用了native声明，在Java的开发里面有一门技术称为JNI技术（JavaNativeInterface），这门技术的特点：使用Java调用本机操作系统提供的函数。但是这样的技术有一个缺点，不能离开特定的操作系统。

如果想要线程能够执行，需要操作系统来进行资源分配，所以此操作严格来讲主要是由JVM负责根据不同的操作系统而实现的。

_**即：使用Thread类的start()方法不仅仅要启动多线程的执行代码，还要去根据不同的操作系统进行资源的分配。**_

```java
```



