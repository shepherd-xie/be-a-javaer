##  线程的同步与死锁

---

1、线程的同步产生原因；

2、线程的同步处理操作；

3、线程的死锁情况。

### 同步问题的引出

实际上所谓的同步指的就是多个线程访问统一资源时所需要考虑到的问题。

**范例：**观察非同步情况下的操作

```java
package com.alpha;
class MyThread implements Runnable {
	private int ticket = 5; // 一共有5张票
	@Override
	public void run() {
		for (int i = 0; i < 20; i ++) {
			if (this.ticket > 0) {
				System.out.println(Thread.currentThread().getName() + "卖票，ticket = " + this.ticket --);
			}
		}
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		new Thread(mt, "票贩子A").start();
		new Thread(mt, "票贩子B").start();
		new Thread(mt, "票贩子C").start();
		new Thread(mt, "票贩子D").start();
	}
}
```

现在四个线程对象应该一起销售出5张票。此时没有出现问题是因为在一个JVM下进行，并且没有受到任何影响，如果要想观察到问题，可以加入一个延迟来看。

```java
class MyThread implements Runnable {
	private int ticket = 5; // 一共有5张票
	@Override
	public void run() {
		for (int i = 0; i < 20; i ++) {
			if (this.ticket > 0) {
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName() + "卖票，ticket = " + this.ticket --);
			}
		}
	}
}
```

此时执行之后发现操作的结果出现了负数，那么这就叫不同步的情况，到底是如何造成不同步呢？

整个卖票的步骤分为两步：

* 第一步：判断是否还有剩余的票数；
* 第二步：减少剩余票数。

### 同步操作

通过观察可以发现以上程序所带来的最大问题在：判断和修改数据是分开完成的，即：某几个线程可以同时执行这些功能。

在Java里面如果想要实现线程的同步可以使用_**synchronized**_关键字。而这个关键字可以通过两种方式使用：

* 一种是同步代码块；
* 另一种是同步方法。

在Java里面有四种代码块：普通代码块、构造块、静态块、同步块。

**范例：**观察同步块

```java
package com.alpha;
class MyThread implements Runnable {
	private int ticket = 5; // 一共有5张票
	@Override
	public void run() {
		for (int i = 0; i < 20; i ++) {
			synchronized(this) { // 当前操作每次只允许一个对象进入
				if (this.ticket > 0) {
					try {
						Thread.sleep(100);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					System.out.println(Thread.currentThread().getName() + "卖票，ticket = " + this.ticket --);
				}
			}
		}
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		new Thread(mt, "票贩子A").start();
		new Thread(mt, "票贩子B").start();
		new Thread(mt, "票贩子C").start();
		new Thread(mt, "票贩子D").start();
	}
}
```

**范例：**使用同步方法解决

```java
package com.alpha;
class MyThread implements Runnable {
	private int ticket = 5; // 一共有5张票
	@Override
	public void run() {
		for (int i = 0; i < 20; i ++) {
			this.sale(); // 调用同步方法
		}
	}
	public synchronized void sale() { // 同步方法
		if (this.ticket > 0) {
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName() + "卖票，ticket = " + this.ticket --);
		}
	}
}
public class MainClass { // 主类
	public static void main(String[] args) throws Exception {
		MyThread mt = new MyThread();
		new Thread(mt, "票贩子A").start();
		new Thread(mt, "票贩子B").start();
		new Thread(mt, "票贩子C").start();
		new Thread(mt, "票贩子D").start();
	}
}
```

同步操作与异步操作相比，异步操作的执行速度要高于同步操作，但是同步操作时数据的安全性较高，属于线程安全的线程操作。

### 死锁

实际上通过分析可以发现，所谓的同步指的就是一个线程对象等待另外一个线程对象执行完毕后的操作形式。线程同步过多就有可能造成死锁。下面编写一个程序，本程序没有任何的实际意义，目的只是表现死锁产生的具体情况。

```java
package com.alpha;
class Chinese {
	public synchronized void say(Foreigner foreigner) {
		System.out.println("把筷子给我，我给你刀叉。");
		foreigner.get();
	}
	public synchronized void get() {
		System.out.println("给出刀叉，得到筷子。");
	}
}
class Foreigner {
	public synchronized void say(Chinese chinese) {
		System.out.println("把刀叉给我，我给你筷子。");
		chinese.get();
	}
	public synchronized void get() {
		System.out.println("给出筷子，得到刀叉。");
	}
}
public class MainClass implements Runnable { // 主类
	private Chinese chinese = new Chinese();
	private Foreigner foreigner = new Foreigner();
	public static void main(String[] args) throws Exception {
		new MainClass();
	}
	public MainClass() {
		new Thread(this).start();
		chinese.say(foreigner);
	}
	@Override
	public void run() {
		foreigner.say(chinese);
	}
}
```

以上的代码只是为了说明死锁而举的一个最无用的例子。本程序没有任何的可参考性。

死锁是程序开发之中由于某种逻辑上的错误所造成的问题，并且不是简单的就会出现的。

**面试题：**请解释多个线程访问同一资源时需要考虑到那些情况？有可能带来那些问题？

* 多个线程访问统一资源时一定要处理好同步，可以使用同步代码块或同步方法来解决；
  * 同步代码块：synchronized(锁定对象) {代码};
  * 同步方法：public synchronized 返回类型 方法名称() {代码}
* 但是过多的使用同步，有可能造成死锁。

#### 总结

1、最简单的理解同步和异步的操作那么就可以通过synchronized来实现；

2、死锁是一种不定的状态。

