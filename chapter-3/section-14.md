## 开发支持类库

* [Arrays类](/chapter-3/section-14.md#Arrays类)
* [UUID类](/chapter-3/section-14.md#UUID类)
* [Optional类](/chapter-3/section-14.md#Optional类)
* [ThreadLocal类](/chapter-3/section-14.md#ThreadLocal类)
* [定时器](/chapter-3/section-14.md#定时器)
* [Base64加密工具](/chapter-3/section-14.md#Base64加密工具)

### Arrays类

在之前一直使用的“java.util.Arrays.sort()”可以实现数组的排序，而Arrays类就是java.util包中提供的一个工具类，这个工具类主要是完成所有与数组有关的操作功能。

在这个类里面存在有二分查找法：public static int binarySearch(Object[] a, int fromIndex, int toIndex, Object key)。但是要进行二分查找前提条件就是先要排序。

**范例：**实现二分查找

```java
package com.alpha;
import java.util.Arrays;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		int[] date = new int[] {1, 5, 6, 2, 3, 4, 9, 8, 7, 10};
		Arrays.sort(date);
		System.out.println(Arrays.binarySearch(date, 9));
	}
}
```

Arrays提供了数组比较：public static boolean equals(Object[] a, Object[] a2)，只不过借用了equals()方法名称与Object类的equals()没有任何关系。

```java
package com.alpha;
import java.util.Arrays;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		int[] datea = new int[] {1, 2, 3};
		int[] dateb = new int[] {1, 2, 3};
		System.out.println(Arrays.equals(datea, dateb));
	}
}
```

要想判断数组是否相同，需要顺序完全一致。

* 填充数组：public static void fill(Object[] a, int fromIndex, int toIndex, Object val)；
* 将数组变为字符串：public static String toString(Object[] a)。

```java
package com.alpha;
import java.util.Arrays;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		int[] date = new int[10];
		Arrays.fill(date, 3);
		System.out.println(Arrays.toString(date));
	}
}
```

### UUID类

通用唯一识别码（英语：Universally Unique Identifier，简称UUID）是一种软件建构的标准，亦为开放软件基金会组织在分布式计算环境领域的一部分。

UUID的目的是让分布式系统中的所有元素都能有唯一的辨识信息，而不需要透过中央控制端来做辨识信息的指定。

* 获取UUID对象：`public static UUID randomUUID()`;
* 根据字符串获取UUID内容：`public static UUID fromString(String name)`;

```java
public class Application {
    public static void main(String[] args) {
        UUID uid = UUID.randomUUID();
        System.out.println(uid.toString());
    }
}
```

### Optional类

Optional类的主要功能是进行NPE的相关处理，在以往进行开发的时候，为了防止程序出现NPE往往需要追加验证。

**范例：**传统的引用传递问题
```java
public class Application {
    public static void main(String[] args) {
        MessageUtil.useMessage(MessageUtil.getMessage());
    }
}
class MessageUtil {
    public static IMessage getMessage() {
        return null;
    }
    public static void useMessage(IMessage msg) {
        if (msg != null) {
            System.out.println(msg.getContent());
        }
    } 
}
interface IMessage {
    String getContent();
}
class MessageImpl implements IMessage {
    @Override
    public String getContent() {
        return "Hello World!";
    }
}
```

在接收引用的时候往往都需要进行NPE的判断，为了解决这个问题，从JD1.8开始提供有Optional类，这个类可以实现NPE的处理操作。

* 返回空数据：`public static <T> Optional<T> empty()`;
* 获取数据：`public T get()`;
* 保存数据，但是不允许为null：`public static <T> Optional<T> of(T value)`;
* 保存数据，允许为null：`public static <T> Optional<T> ofNullable(T value)`;
* 为空返回默认值：`public T orElse(T other)`;

**范例：**修改程序
```java
public class Application {
    public static void main(String[] args) {
        IMessage msg = MessageUtil.getMessage().get();
        MessageUtil.useMessage(msg);
    }
}
class MessageUtil {
    public static Optional<IMessage> getMessage() {
        return Optional.of(new MessageImpl());
    }
    public static void useMessage(IMessage msg) {
        if (msg != null) {
            System.out.println(msg.getContent());
        }
    } 
}
interface IMessage {
    String getContent();
}
class MessageImpl implements IMessage {
    @Override
    public String getContent() {
        return "Hello World!";
    }
}
```

### ThreadLocal类

ThreadLocal是一个关于创建线程局部变量的类。

通常情况下，我们创建的变量是可以被任何一个线程访问并修改的。而使用ThreadLocal创建的变量只能被当前线程访问，其他线程则无法访问和修改。

**范例：**定义一个发送消息的功能
```java
public class Application {
    public static void main(String[] args) {
        Message message = new Message();
        message.setInfo("Hello World!");
        Channel.setMessage(message);
        Channel.send();
    }
}
class Channel {
    private static Message message;
    public static void setMessage(Message message) {
        Channel.message = message;
    }
    public static void send() {
        System.out.println("[SEND] " + message.getInfo());
    }
}
class Message {
    private String info;
    public void setInfo(String info) {
        this.info = info;
    }
    public String getInfo() {
        return info;
    }
}
```

以上的程序采用的是单线程的处理方法，如果在多线程的环境下还能否正常运行？

**范例：**在多线程环境下运行
```java
public class Application {
    public static void main(String[] args) {
        new Thread(() -> {
            Message message = new Message();
            message.setInfo("Hello Thread A!");
            Channel.setMessage(message);
            Channel.send();
        }, "Thread A").start();
        new Thread(() -> {
            Message message = new Message();
            message.setInfo("Hello Thread B!");
            Channel.setMessage(message);
            Channel.send();
        }, "Thread B").start();
        new Thread(() -> {
            Message message = new Message();
            message.setInfo("Hello Thread C!");
            Channel.setMessage(message);
            Channel.send();
        }, "Thread C").start();
    }
}
class Channel {
    private static Message message;
    public static void setMessage(Message message) {
        Channel.message = message;
    }
    public static void send() {
        System.out.println("[SEND] " + message.getInfo());
    }
}
class Message {
    private String info;
    public void setInfo(String info) {
        this.info = info;
    }
    public String getInfo() {
        return info;
    }
}
```

发现此时程序出现了较大的影响，线程与线程之间产生了干扰。在保持Channel核心结构不变的情况下，需要考虑到每个线程独立操作的问题。Channel所保存的消息相对于线程来讲应是线程私有的，此时就可以通过ThreadLocal类来存放数据。

* 构造方法：`public ThreadLocal()`;
* 设置数据：`public void set(T value)`;
* 取出数据：`public T get()`;
* 删除数据：`public void remove()`;

**范例：**解决线程同步问题
```java
public class Application {
    public static void main(String[] args) {
        new Thread(() -> {
            Message message = new Message();
            message.setInfo("Hello Thread A!");
            Channel.setMessage(message);
            Channel.send();
        }, "Thread A").start();
        new Thread(() -> {
            Message message = new Message();
            message.setInfo("Hello Thread B!");
            Channel.setMessage(message);
            Channel.send();
        }, "Thread B").start();
        new Thread(() -> {
            Message message = new Message();
            message.setInfo("Hello Thread C!");
            Channel.setMessage(message);
            Channel.send();
        }, "Thread C").start();
    }
}
class Channel {
    private static final ThreadLocal<Message> THREAD_LOCAL = new ThreadLocal<>();
    public static void setMessage(Message message) {
        THREAD_LOCAL.set(message);
    }
    public static void send() {
        System.out.println("[" + Thread.currentThread().getName() + " SEND] " + THREAD_LOCAL.get().getInfo());
    }
}
class Message {
    private String info;
    public void setInfo(String info) {
        this.info = info;
    }
    public String getInfo() {
        return info;
    }
}
```

### 定时器

定时器的主要功能是进行任务的定时操作，在特定的时间执行特定的任务。在Java中提供有定时器的支持，但是这种任务的处理知识实现了一种间隔触发的操作。

如果要想实现定时任务需要有一个定时操作的主体类，以及一个定时任务的控制。可以使用两个类实现：
* `java.util.TimerTask`：实现定时任务；
* `java.util.Timer`：进行任务的启动：
  * 任务启动：`public void schedule(TimerTask task, long delay)`；
  * 间隔触发：`public void scheduleAtFixedRate(TimerTask task, long delay, long period)`；
  
**范例：**定时任务
```java
public class Application {
    public static void main(String[] args) {
        Timer timer = new Timer();
        timer.scheduleAtFixedRate(new MyTask(), 100, 1000);
    }
}
class MyTask extends TimerTask {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " 定时任务执行，当前时间：" + System.currentTimeMillis());
    }
}
```

### Base64加密工具

加密操作是如今应用必不可少的组成部分，JDK1.8开始提供了一组新的加密处理操作，Base64处理，在这个类里面有两个内部类：

* `Base64.Encoder`：进行加密处理；
  * 加密：`public byte[] encode(byte[] src)`;
* `Base64.Decoder`：进行解密处理；
  * 解密：`public byte[] decode(byte[] src)`;

**范例：**实现加密解密操作
```java
public class Application {
    public static void main(String[] args) {
        String msg = "Hello World!";
        String encMsg = new String(Base64.getEncoder().encode(msg.getBytes()));
        System.out.println(encMsg);
        String oldMsg = new String(Base64.getDecoder().decode(encMsg));
        System.out.println(oldMsg);
    }
}
```
