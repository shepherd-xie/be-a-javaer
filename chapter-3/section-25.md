## 集合输出

集合在JDK 1.8之前支持四种输出：Iterator（95%）、ListIterator（0.05%）、Enumeration（4.9%）、foreach（0.05%）。

### 迭代输出：Iterator（核心）

如果遇见了集合操作，那么一般而言都会使用Iterator接口进行集合的输出操作，首先来观察Iterator接口的定义。

```java
public interface Iterator<E> {
	public boolean hasNext();
	public E next();
}
```

Iterator本身是一个接口，如果要想取得本接口实例化只能够依靠Collection接口，在Collection接口里面定义有如下的一个操作方法：public Iterator<E> iterator()；

**范例：**使用Iterator输出集合

```java
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Set<String> all = new HashSet<String>();
		all.add("逸");
		all.add("洱");
		all.add("洱");
		Iterator<String> iter = all.iterator();
		while (iter.hasNext()) {
			String str = iter.next();
			System.out.println(str);
		}
	}
}
```

以后集合的输出直接使用Iterator就行了。

### 双向迭代：ListIterator（了解）

Iterator本身只具有由前向后的输出，但是有些人认为应该让其支持双向的输出，即：可以实现由前向后，也可以实现由后向前。那么就可以使用Iterator的子接口 —— ListIterator接口，在这个接口里面主要是两个方法：

* 判断是否有前一个元素：public boolean hasPrevious()；
* 取得前一个元素：public E previous()；

ListIterator是专门为List子接口定义的输出接口，方法：public ListIterator<E> listIterator()。

**范例：**完成双向迭代

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.ListIterator;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("逸");
		all.add("洱");
		all.add("叁");
		System.out.print("由前向后输出：");
		ListIterator<String> iter = all.listIterator();
		while (iter.hasNext()) {
			String str = iter.next();
			System.out.print(str + "、");
		}
		System.out.print("\n由后向前输出：");
		while (iter.hasPrevious()) {
			System.out.print(iter.previous() + "、");
		}
	}
}
```

如果要想实现由后向前的输出之前，一定要首先发生由前向后的输出。

### foreach输出

理论上而言，foreach输出是一种十分方便的方式，但是如果过多的使用foreach不利于理解程序。foreach可以方便的输出数组，但是集合也可以。

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("逸");
		all.add("洱");
		all.add("叁");
		for (String str : all) {
			System.out.println(str);
		}
	}
}
```

在初学阶段还是强烈建议使用Iterator操作。

### Enumeration输出

Enumeration是与Vector类一起在JDK 1.0的时候退出的输出接口，即：最早的Vector如果要想输出就需要使用Enumeration接口完成，此接口定义如下：

```java
public interface Enumeration<E> {
	public boolean hasMoreElements(); // 判断是否有下一个元素，等同于hasNext()
	public E nextElement(); // 取出当前元素，能同于next()
}
```

但是如果要想取得Enumeration接口的实例化对象只能够依靠Vector子类。在Vector子类里面定义有如下的操作方法：

* 取得Enumeration接口对象：public Enumeration<E> elements()；

```java
package com.alpha.demo;
import java.util.Enumeration;
import java.util.Vector;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Vector<String> all = new Vector<String>();
		all.add("逸");
		all.add("洱");
		all.add("叁");
		Enumeration<String> enu = all.elements();
		while (enu.hasMoreElements()) {
			System.out.println(enu.nextElement());
		}
	}
}
```

在一些古老的操作方法上，此接口依然会使用到，所以必须掌握。
