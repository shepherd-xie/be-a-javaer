## 集合工具类

* [Stack](/chapter-3/section-28.md#stack)
* [Queue](/chapter-3/section-28.md#queue)
* [Properties](/chapter-3/section-28.md#properties)
* [Collections工具类](/chapter-3/section-28.md#collections工具类)

### Stack

`Stack`表示的是栈操作，栈是一种先进后出的数据结构。而`Stack`是`Vector`的子类。

```java
public class Stack<E>
extends Vector<E>
```

虽然Stack是Vector子类，可是它不会使用Vector类的方法，它使用自己的方法：
* 入栈：`public E push(E item)`；
* 出栈：`public E pop()`；

**范例：** 观察栈的操作
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		Stack<String> all = new Stack<String>();
		all.push("A");
		all.push("B");
		all.push("C");
		System.out.println(all.pop());
		System.out.println(all.pop());
		System.out.println(all.pop());
		System.out.println(all.pop()); // EmptyStackException
	}
}
```

在进行栈操作的过程之中，如果栈已经没没有数据了，那么无法继续出栈。

### Queue

`Quene`描述的是一个队列，队列的特点就是先进先出，常见的使用场景有生产队列、消息队列等。如果将队列应用在多线程的“生产者与消费者”模型上，那么对于生产者生产过快的情况就不会再等待消费者取出数据。队列的实现常用的是`LinkedList`。

队列的使用主要依靠`Queue`接口之中提供的两个方法：
* 追加：`public boolean offer(E e)`，可以直接使用`add()`方法；
* 获取：`public E poll()`。

**范例：** 实现队列操作
```java
public class Application {
    public static void main(String[] args) throws Exception {
        Queue<String> queue = new LinkedList<>();
        queue.offer("X");
        queue.offer("Y");
        queue.offer("Z");
        System.out.println(queue.poll());
        System.out.println(queue.poll());
        System.out.println(queue.poll());
    }
}
```

### Properties

`Properties`是`Hashtable`的子类，主要是进行属性的操作（属性的最大特点是利用字符串设置`key`和`value`）。观察`Properties`类的定义结构：

```java
public class Properties
extends Hashtable<Object,Object>
```

在使用Properties类的时候不需要设置泛型类型，因为从它一开始出现就只能够保存String。在Properties类里面主要使用如下的操作方法：

* 设置属性：`public Object setProperty(String key, String value)`；
* 取得属性：`public String getProperty(String key)`，如果`key`不存在返回`null`；
* 取得属性：`public String getProperty(String key, String defaultValue)`，如果`key`不存在返回`defaultValue`；

**范例：** 属性的基本操作
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		Properties pro = new Properties();
		pro.setProperty("BJ", "北京");
		pro.setProperty("TZ", "天津");
		System.out.println(pro.getProperty("BJ"));
		System.out.println(pro.getProperty("GZ"));
		System.out.println(pro.getProperty("GZ", "没有此项"));
	}
}
```

在`Properties`类里面提供有数据的输出操作：`public void store(OutputStream out, String comments) throws IOException`。

**范例：** 将属性信息保存在文件里
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		Properties pro = new Properties();
		pro.setProperty("BJ", "北京");
		pro.setProperty("TZ", "天津");
		// 一般来说后缀可以随意设置，但是从标准上讲后缀必须是*.properties
		pro.store(new FileOutputStream("area.properties"), "Area Info");
	}
}
```

也可以从指定的输入流中读取属性信息：`public void load(InputStream inStream) throws IOException`；

**范例：** 通过文件流读取属性内容
```java
package com.alpha.demo;
import java.io.FileInputStream;
import java.util.Properties;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		Properties pro = new Properties();
		pro.load(new FileInputStream("area.properties"));
		System.out.println(pro.getProperty("BJ"));
	}
}
```

对于属性文件（资源文件）而言，除了可以使用`Properties`类读取之外，也可以使用`ResourceBundle`类读取。

### Collections工具类

在Java提供类库的时候考虑到用户的使用方便性，所以专门提供了一个集合的工具类 —— `Collections`，这个工具类可以实现`List`、`Set`、`Map`集合的操作。

* 为集合追加数据：`public static <T> boolean addAll(Collection<? super T> c, T... elements)`；
* 翻转集合：`public static void reverse(List<?> list)`；

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		Collections.addAll(all, "A", "B", "C", "D", "E");
		Collections.reverse(all);
		System.out.println(all);
	}
}
```

**面试题：**请解释`Collection`与`Collections`的区别？
* `Collection`是集合操作的接口；
* `Collections`是集合操作的工具类，可以进行`List`、`Set`、`Map`集合的操作。
