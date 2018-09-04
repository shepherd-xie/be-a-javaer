##  线程常用操作方法

---

多线程有很多的方法定义，但是大部分的方法都是在Thread类里面定义的，强调几个与开发有关的方法。

### 线程的命名与取得

所有的线程程序的执行，每一次都是不同的运行结果，因为它会根据自己的情况进行资源抢占，如果要想区分每一个线程，那么就必须要依靠线程的名字。对于线程名字一般会在其启动之前进行定义，不建议对已经启动的线程进行更改名称，或者是为不同的线程设置重名的情况。

如果要想进行线程名称的操作，可以使用Thread类的如下方法：

* 构造方法：public Thread(Runnable target, String name)
* 设置名字：public final void setName(String name)
* 取得名字：public final String getName()

对于线程名字的操作会出现一个问题，这些方法是属于Thread类里面的，可是如果在线程类（Runnable）子类，这个类里面并没与继承Thread类，如果要想取得名字，那么能够取得的就当前执行本方法的线程名字。所以在Thread类里面提供有一个方法：

* 取得当前线程对象：public static Thread currentThread()

**范例：** 观察线程的命名

```java
package com.alpha;
class MyThread implements Runnable {
	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName());
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		new Thread(mt).start();
		new Thread(mt).start();
		new Thread(mt).start();
	}
}
```

如果在实例化Thread类对象的时候没有为其设置名字，那么会自动的进行编号命名，也就是说保证线程对象的名字不重复。

**范例：** 设置名字

```java
package com.alpha;
class MyThread implements Runnable {
	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName());
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		new Thread(mt, "自己的线程A").start();
		new Thread(mt).start();
		new Thread(mt, "自己的线程B").start();
	}
}
```

下面来观察如下的一个程序执行。

```java
package com.alpha;
class MyThread implements Runnable {
	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName());
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		new Thread(mt, "自己的线程对象").start();
		mt.run();
	}
}
```

```java
main
自己的线程对象
```

原来主方法就是一个线程（main线程），那么所有在主方法上创建的线程实际上都可以将其表示为子线程。

通过以上的代码可以发现，线程实际上一直都存在（主方法就是主线程），可是进程去哪里了呢？

每当使用java命令去解释一个程序类的时候，对于操作系统而言，都相当于启动了一个新的进程，而main只是这个新进程上的一个子线程。

**提问：**每一个JVM进程启动的时候至少启动几个线程

* main线程：程序的主要执行，以及启动子线程；
* gc线程：负责垃圾收集。

### 线程的休眠

所谓的线程休眠指的就是让线程的执行速度稍微变慢一点。休眠的方法：

* public static void sleep(long millis) throws InterruptedException
* public static void sleep(long millis, int nanos) throws InterruptedException

**范例：** 观察休眠特点

```java
package com.alpha;
class MyThread implements Runnable {
	@Override
	public void run() {
		for (int i = 0; i < 10000; i ++) {
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName() + ", i = " + i);
		}
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		new Thread(mt, "自己的线程对象A").start();
	}
}
```

因为每一次执行到run()方法的线程对象都必须进行休眠，所以执行的速度就会变慢。

默认情况下，在休眠的时候如果设置了多个线程对象，那么多有的线程对象将一起进入到run()方法（所谓的一起进入实际上是因为先后顺序实在是太短了，但实际上有区别。）。

### 线程的中断

在之前的休眠方法中抛出了一个中断异常InterruptedException，实际上线程是可以被其他线程中断的，Thread类提供以下方法：
* 判断线程是否被中断：public boolean isInterrupted()
* 中断线程：public void interrupt()

**范例：** 线程的中断
```java
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
        Thread thread = new Thread(() -> {
            System.out.println("线程休眠");
            try {
                Thread.sleep(10000);
                System.out.println("休眠结束");
            } catch (InterruptedException e) {
                System.out.println("产生中断");
            }
        });
        thread.start();
        Thread.sleep(100);
        if (!thread.isInterrupted()) {
            System.out.println("中断线程");
            thread.interrupt();
        }
	}
}
```

### 线程合并

线程合并就是将几个并发线程合并为一个单一线程执行，应用场景就是当一个线程的执行必须是要等到其他线程执行完毕之后才能执行。
* public final void join() throws InterruptedException
* public final void join(long millis) throws InterruptedException
* public final void join(long millis, int nanos) throws InterruptedException

**范例：** 线程合并
```java
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
        Thread mainThread = Thread.currentThread();
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 50; i ++) {
                if (i == 20) {
                    try {
                        mainThread.join();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + " ==> " + i);
            }
        });
        thread.start();
        for (int i = 0; i < 50; i ++) {
            Thread.sleep(100);
            System.out.println(Thread.currentThread().getName() + " ==> " + i);
        }
	}
}
```

### 线程让步

如果当前线程获得CPU时间片可以使用线程让步将CPU时间片让渡给其他线程，而自身进入就绪阶段。
* public static void yield()

**范例：** 线程让步
```java
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
        Thread mainThread = Thread.currentThread();
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 50; i ++) {
                if (i % 3 == 0) {
                    System.out.println(Thread.currentThread().getName() + " 让步");
                    Thread.yield();
                }
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + " ==> " + i);
            }
        });
        thread.start();
        for (int i = 0; i < 50; i ++) {
            Thread.sleep(100);
            System.out.println(Thread.currentThread().getName() + " ==> " + i);
        }
	}
}
```

### 线程优先级

所谓的优先级指的是越高的优先级，越有可能先执行。在Thread类里面提供有以下的两个方法进行优先级操作。

* 设置优先级：public final void setPriority(int newPriority)；
* 取得优先级：public final int getPriority()。

发现设置和取得优先级都是使用了int数据类型，对于此内容有三种取值：

* 最高优先级：public static final int MAX_PRIORITY，10；
* 中等优先级：public static final int NORM_PRIORITY，5；
* 最低优先级：public static final int MIN_PRIORITY，1。

**范例：** 测试

```java
package com.alpha;
class MyThread implements Runnable {
	@Override
	public void run() {
		for (int i = 0; i < 20; i ++) {
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName() + ", i = " + i);
		}
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		Thread t1 = new Thread(mt, "自己的线程对象A");
		Thread t2 = new Thread(mt, "自己的线程对象B");
		Thread t3 = new Thread(mt, "自己的线程对象C");
		t1.setPriority(Thread.MAX_PRIORITY);
		t1.start();
		t2.start();
		t3.start();
	}
}
```

**范例：** 主线程优先级是多少？

```java
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		System.out.println(Thread.currentThread().getPriority());
	}
}
```

主线程属于中等优先级。

#### 总结

1、Thread.currentThread可以取得当前线程类对象；

2、Thread.sleep()主要是休眠，感觉是一起休眠，但实际上是有先后顺序的；

3、优先级越高的线程对象越有可能先执行。

