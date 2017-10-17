##  生产者与消费者

---

1、生产者和消费者问题的产生；

2、Object类对多线程的支持。

### 问题的引出

生产者和消费者是的是两个不同的线程类对象，操作同一资源的情况。

* 生产者负责生产数据，消费者负责取走数据；
* 生产者每生产完一组数据之后，消费者就要取走一组数据。

那么现在假设要生产的数据如下：

* 第一组数据：title=mori，content=好学生
* 第二组数据：title=可爱的萌动物，content=草泥马

**范例：**程序基本模型

```java
package com.alpha;
class Info {
	private String title;
	private String content;
	public void setTitle(String title) {
		this.title = title;
	}
	public String getTitle() {
		return title;
	}
	public void setContent(String content) {
		this.content = content;
	}
	public String getContent() {
		return content;
	}
}
class Productor implements Runnable {
	private Info info;
	public Productor(Info info) {
		this.info = info;
	}
	@Override
	public void run() {
		for (int i = 0; i < 100; i ++) {
			if (i % 2 == 0) {
				this.info.setTitle("mori");
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				this.info.setContent("好学生");
			} else {
				this.info.setTitle("可爱的萌动物");
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				this.info.setContent("草泥马");
			}
		}
	}
}
class Customer implements Runnable {
	private Info info;
	public Customer(Info info) {
		this.info = info;
	}
	@Override
	public void run() {
		for (int i = 0; i < 100; i ++) {
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(this.info.getTitle() + " - " + this.info.getContent());
		}
	}
}
public class MainClass{ // 主类
	public static void main(String[] args) {
		Info info = new Info();
		new Thread(new Productor(info)).start();
		new Thread(new Customer(info)).start();
	}
}
```

现在实际上通过以上的代码可以发现两个严重问题：

* 数据错位，发现不再是一个所需要的完整数据；
* 数据重复取出，数据重复设置。

### 解决数据错乱问题

数据的错位完全是因为非同步的操作所导致的，所以应该使用同步处理。因为取和设置是两个不同的操作，所以要想进行同步控制，那么就需要将其定义在一个类里面完成。

```java
package com.alpha;
class Info {
	private String title;
	private String content;
	public synchronized void set(String title, String content) {
		this.title = title;
		try {
			Thread.sleep(200);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		this.content = content;
	}
	public synchronized void get() {
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println(this.title + " - " + this.content);
	}
}
class Productor implements Runnable {
	private Info info;
	public Productor(Info info) {
		this.info = info;
	}
	@Override
	public void run() {
		for (int i = 0; i < 100; i ++) {
			if (i % 2 == 0) {
				this.info.set("mori", "好学生");
			} else {
				this.info.set("可爱的萌动物", "草泥马");
			}
		}
	}
}
class Customer implements Runnable {
	private Info info;
	public Customer(Info info) {
		this.info = info;
	}
	@Override
	public void run() {
		for (int i = 0; i < 100; i ++) {
			this.info.get();
		}
	}
}
public class MainClass{ // 主类
	public static void main(String[] args) {
		Info info = new Info();
		new Thread(new Productor(info)).start();
		new Thread(new Customer(info)).start();
	}
}
```

此时数据的错位问题很好的得到了解决，但是重复操作问题更加严重了。

### 解决重复的问题

如果想要实现整个代码的操作，必须加入等待与唤醒机制，在Object类里面提供有专门的处理方法：

* 等待：public final void wait() throws InterruptedException；
* 唤醒第一个等待线程：public final void notify()；
* 唤醒全部等待线程，那个优先级高就先执行：public final void notifyAll()。

**范例：**解决程序问题

```java
package com.alpha;
class Info {
	private String title;
	private String content;
	private boolean flag = true;
	// flag = true：表示可以生产，但是不可以取走
	// flag = false：表示可以取走，但是不可以生产
	public synchronized void set(String title, String content) {
		// 重复进入到了set()方法里面，发现不能够生产，所以要等待
		if (this.flag == false) {
			try {
				super.wait();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		this.title = title;
		try {
			Thread.sleep(200);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		this.content = content;
		this.flag = false; // 修改生产标记
		super.notifyAll(); // 唤醒其他等待线程
	}
	public synchronized void get() {
		if (this.flag == true) {
			try {
				super.wait();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println(this.title + " - " + this.content);
		this.flag = true;
		super.notifyAll();
	}
}
class Productor implements Runnable {
	private Info info;
	public Productor(Info info) {
		this.info = info;
	}
	@Override
	public void run() {
		for (int i = 0; i < 100; i ++) {
			if (i % 2 == 0) {
				this.info.set("mori", "好学生");
			} else {
				this.info.set("可爱的萌动物", "草泥马");
			}
		}
	}
}
class Customer implements Runnable {
	private Info info;
	public Customer(Info info) {
		this.info = info;
	}
	@Override
	public void run() {
		for (int i = 0; i < 100; i ++) {
			this.info.get();
		}
	}
}
public class MainClass{ // 主类
	public static void main(String[] args) {
		Info info = new Info();
		new Thread(new Productor(info)).start();
		new Thread(new Customer(info)).start();
	}
}
```

**面试题：**请解释sleep()与wait()的区别？

* sleep()是Thread类定义的方法，wait()是Object类定义的方法；
* sleep()可以设置休眠时间，时间一到自动唤醒，而wait()需要等待notify()进行唤醒。

#### 总结

这是一个非常经典的多线程的处理模型。

