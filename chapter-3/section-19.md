## 类集框架

---

### 类集简介（了解）

类集就是一组Java实现的数据结构。或者再简单一点，所谓的类集指的就是对象数组的应用。

在以前的例子中，要想保存多个对象，应该使用对象数组，但是传统的对象数组有一个问题：长度是固定的（数组一般不会使用）。后来使用链表来实现了一个动态的对象数组，但是对于链表的开发有几个问题：

* 链表的开发实在是太麻烦；
* 如果要考虑到链表的操作性能太麻烦；
* 链表使用了Object类进行保存，所有的对象必须发生向上以及强制性的向下转型。

综合以上的问题得出结论：如果在开发项目里面由用户自己去实现一个链表，那么这种项目的开发难度实在是太大了。并且在所有的项目里面都会存在有数据结构的应用，那么在Java设计之初就考虑到了此类问题，所以提供了一个与链表类似的工具类 —— Vector（向量），但是后来随着时间的推移，发现这个类并不能够很好的描述出数据结构这一概念，所以从Java 2（JDK 1.2之后）提供了一个专门实现数据结构的开发框架 —— 类集框架，并且在JDK 1.5之后，由于泛型技术的引入，有解决了类集框架之中所有的操作类型都使用Object所带来的安全隐患。

随后在JDK 1.8里面，又针对于类集的大数据的操作环境下推出了数据流的分析操作功能。

在整个类集里面一共有以下的几个核心接口：

* Collection、List、Set；
* Map；
* Iterator，Enumeration。

### Collection接口（重点）

Collection是整个类集之中单值保存的最大父接口。即：每一次可以向集合里面保存一个对象。首先来观察Collection接口的定义：

```java
public interface Collection<E>
extends Iterable<E>
```

在Collection接口里面定义有如下的几个常用操作方法。

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public boolean add(E e) | 普通 | 向集合里面保存数据 |
| 2 | public boolean addAll(Collection<? extends E> c) | 普通 | 追加一个集合 |
| 3 | public void clear() | 普通 | 清空集合 |
| 4 | public boolean contains(Object o) | 普通 | 判断是否包含有指定的内容 |
| 5 | public boolean isEmpty() | 普通 | 判断集合是否为空 |
| 6 | public boolean remove(Object o) | 普通 | 删除对象 |
| 7 | public int size() | 普通 | 取得集合中保存的元素个数 |
| 8 | public Object[] toArray() | 普通 | 将集合变为对象数组保存 |
| 9 | public Iterator<E> iterator() | 普通 | 为Iterator接口实例化 |

在所有的开发之中add()与iterator()两个方法的使用几率是最高的，其他的方法几乎可以忽略，但是你必须知道。需要注意的是，在使用contains()与remove()两个方法一定要依靠equals()支持。

按照以往对接口的使用，要想使用Collection接口就应该使用子类实例化这个接口。但是出于对程序严谨性的考虑，不会再直接使用Collection接口，而是使用它的两个子接口：List（允许重复）、Set（不允许重复）。

> **历史回顾：**
> 
> 最早Java刚刚推出类集框架的时候，使用最多的就是Collection接口，最大的使用环境就是在EJB上。于是在Java的一个开源项目（PetShop）上出现了问题。由于此项目是属于Java业余爱好者共同开发的，所以没有考虑到过多的性能问题以及代码或数据库的设计，就导致了整个程序的技术高端、性能过差，此时正赶上微软准备推出.net平台。微软使用.net重新设计并且开发了PetShop，对外宣布性能比Java好，于是事情的本事发生了改变，人们开始认为.net平台的性能很高，后来SUN的官方重新编写了PetShop，并且发布了测试报告，但是由于此时微软的宣传，所以基本上就已经变成了性能上的差距事实。
> 
> 因为代码的规范化的产生，所以从PetShop开始就不在使用模糊不清的Collection接口了，而都使用List或Set子接口进行开发。

### List子接口

List子接口是Collection子接口中最为常用（80%）的一个子接口，但是这个接口对Collection接口进行了一些功能的扩充。在List子接口里面重点掌握以下方法的使用：

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public E get(int index) | 普通 | 取得索引编号的内容 |
| 2 | public E set(int index, E element) | 普通 | 修改指定索引编号的内容 |
| 3 | public ListIterator<E> listIterator() | 普通 | 为ListIterator接口实例化 |

而List本身是属于接口，所以如果要想使用此接口进行操作，那么就必须存在有子类，可以使用ArrayList子类实现操作（还有另一个Vector子类，90%的情况下选择的是ArrayList）。

### 新的子类：ArrayList

ArrayList类是List接口最为常用的一个子类。下面将利用此类来验证之前所描述过的操作方法。

**范例：**List基本操作

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		// 设置了泛型，从而保证集合中所有数据类型都一致
		List<String> all = new ArrayList<String>();
		System.out.println("长度：" + all.size());
		System.out.println("是否为空：" + all.isEmpty());
		all.add("Hello");
		all.add("Hello"); // 重复元素
		all.add("World");
		System.out.println("长度：" + all.size());
		System.out.println("是否为空：" + all.isEmpty());
		// Collection接口定义了size()方法可以取得集合长度
		// List子接口扩充了一个get()方法，可以根据索引取得数据
		for (int i = 0; i < all.size(); i ++) {
			String str = all.get(i); // 按索引取得数据
			System.out.println(str);
		}
	}
}
```

可以发现，List集合之中所保存的数据是按照保存的顺序存放，而且允许存在有重复数据，但是List子接口扩充有get()方法。

**范例：**为Collection接口实例化

* ArrayList是List接口子类，而List有事Collection子接口，自然可以通过ArrayList为Collection接口实例化。

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.Collection;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Collection<String> all = new ArrayList<String>();
		all.add("Hello");
		all.add("Hello"); // 重复元素
		all.add("World");
		Object[] obj = all.toArray();
		for (int i = 0; i < obj.length; i ++) {
			System.out.println(obj[i]);
		}
	}
}
```

Collection接口与List接口相比，功能会显得有所不足，而且以上的形式并不是集合所使用的标准的输出的结构，只是一个基础的展示。

**范例：**在集合里面保存对象

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (!(obj instanceof Book))
			return false;
		Book other = (Book) obj;
		if (price != other.price)
			return false;
		if (title == null) {
			if (other.title != null)
				return false;
		} else if (!title.equals(other.title))
			return false;
		return true;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]\n";
	}
}
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		List<Book> all = new ArrayList<Book>();
		all.add(new Book("Java", 79.8));
		all.add(new Book("JSP", 69.8));
		all.add(new Book("Oracle", 89.8));
		// 任何情况下集合数据的删除与内容的查询都必须提供有equals()方法
		all.remove(new Book("Oracle", 89.8));
		System.out.println(all);
	}
}
```

与之前的链表相比，几乎是横向替代就是替换了一个类名称而已，因为给出的链表就是按照Collection与List接口的方法标准定义的。

### 旧的子类：Vector

在最早JDK 1.0的时候就已经提供有Vector类，并且这个类被大量的使用。但是到了JDK 1.2的时候由于类集框架的引入，所以对于整个集合的操作就有了新的标准，那么为了可以继续保留下Vector类，所以让这个类多实现了一个List接口。

**范例：**使用Vector

```java
package com.alpha.demo;
import java.util.List;
import java.util.Vector;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		// 设置了泛型，从而保证集合中所有数据类型都一致
		List<String> all = new Vector<String>();
		System.out.println("长度：" + all.size());
		System.out.println("是否为空：" + all.isEmpty());
		all.add("Hello");
		all.add("Hello"); // 重复元素
		all.add("World");
		System.out.println("长度：" + all.size());
		System.out.println("是否为空：" + all.isEmpty());
		// Collection接口定义了size()方法可以取得集合长度
		// List子接口扩充了一个get()方法，可以根据索引取得数据
		for (int i = 0; i < all.size(); i ++) {
			String str = all.get(i); // 按索引取得数据
			System.out.println(str);
		}
	}
}
```

**面试题：**请解释ArrayList与Vector的区别？

| No. | 区别 | ArrayList（90%） | Vector（10%） |
| :---: | :---: | :---: | :---: |
| 1 | 推出时间 | JDK 1.2时推出，属于新的类 | JDK 1.0时推出，属于旧的类 |
| 2 | 性能 | 采用异步处理 | 采用同步处理 |
| 3 | 数据安全 | 非线程安全 | 线程安全 |
| 4 | 输出 | Iterator、ListIterator、foreach | Iterator、ListIterator、foreach、Enumeration |

在以后的开发之中，如果使用了List子接口就使用ArrayList子接口。

### Set子接口

在Collection接口下又有另外一个比较常用的子接口为Set接口（20%），Set接口并不像List接口那样对于Collection接口进行了大量的扩充，而是简单的继承了Collection接口。也就没有了之前List集合所提供的的get()方法。

Set接口下有两个常用的子类：HashSet、TreeSet。

**范例：**观察HashSet子类的特点

```java
package com.alpha.demo;
import java.util.HashSet;
import java.util.Set;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Set<String> all = new HashSet<String>();
		all.add("Mori");
		all.add("Hello");
		all.add("Hello"); // 重复数据
		all.add("World");
		System.out.println(all);
	}
}
```

通过代码可以发现，Set集合下没有重复元素（这一点是Set接口的特征），同时发现在里面所保存的数据是没有任何顺序的，即：HashSet子类的特征属于无需排列。

**范例：**使用TreeSet子类

```java
package com.alpha.demo;
import java.util.Set;
import java.util.TreeSet;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Set<String> all = new TreeSet<String>();
		all.add("M");
		all.add("B");
		all.add("B"); // 重复数据
		all.add("A");
		System.out.println(all);
	}
}
```

此时的程序使用了TreeSet子类，发现没有重复数据，并且所保存的内容自动排序。

### 关于数据排序的说明

既然TreeSet子类保存的内容可以进行排序，那么下面不如就编写一个自定义的类来完成数据的保存。

集合就是一个动态的对象数组，那么如果想要为一组对象进行排序，在Java里面必须要使用比较器，应该使用Comparable完成比较。在比较方法里面需要将这个类的所有属性都一起参与到比较之中。

**范例：**TreeSet排序

```java
package com.alpha.demo;
import java.util.Set;
import java.util.TreeSet;
class Book implements Comparable<Book> {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]\n";
	}
	@Override
	public int compareTo(Book o) {
		if (this.price > o.price)
			return 1;
		else if (this.price < o.price)
			return -1;
		if (this.title == null) 
			if (o.title != null)
				return -1;
			else
				return 0;
		return this.title.compareTo(o.title); // 调用了String类的比较大小
	}
}
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Set<Book> all = new TreeSet<Book>();
		all.add(new Book("Java", 69.8));
		all.add(new Book("Java", 69.8)); // 全部属性相同
		all.add(new Book("JSP", 69.8)); // 部分属性相同
		all.add(new Book("Oracle", 79.8)); // 全都不同
		System.out.println(all);
	}
}
```

通过检测可以发现TreeSet类主要是依靠Comparable接口中的compareTo()方法判断是否是重复数据，如果返回的是0，那么就认为是重复数据，不会被保存。

### 关于重复元素的说明

Comparable接口只能够负责TreeSet子类进行重复元素的判断，它并不是真正的用于能够进行重复元素验证的操作。如果要想判断重复元素那么只能够依靠Object类中所提供的方法：

* 取得哈希码：public int hashCode()；
  * 先判断对象的哈希码是否相同，依靠哈希码取得一个对象的内容；
* 对象比较：public boolean equals(Object obj)；
  * 再讲对象的属性进行依次的比较；

```java
package com.alpha.demo;
import java.util.HashSet;
import java.util.Set;
class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		long temp;
		temp = Double.doubleToLongBits(price);
		result = prime * result + (int) (temp ^ (temp >>> 32));
		result = prime * result + ((title == null) ? 0 : title.hashCode());
		return result;
	}
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Book other = (Book) obj;
		if (Double.doubleToLongBits(price) != Double.doubleToLongBits(other.price))
			return false;
		if (title == null) {
			if (other.title != null)
				return false;
		} else if (!title.equals(other.title))
			return false;
		return true;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]\n";
	}
}
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Set<Book> all = new HashSet<Book>();
		all.add(new Book("Java", 69.8));
		all.add(new Book("Java", 69.8)); // 全部属性相同
		all.add(new Book("JSP", 69.8)); // 部分属性相同
		all.add(new Book("Oracle", 79.8)); // 全都不同
		System.out.println(all);
	}
}
```

以后在非排序的情况下，只要是判断重复元素依靠的永远都是hashCode()与equals()。

### 集合输出

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
package com.alpha.demo;
import java.util.HashSet;
import java.util.Iterator;
import java.util.Set;
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

### Map接口

Collection每一次都只会保存一个对象，而Map主要是负责保存一对对象的信息。

如果说现在要保存一对关联数据（key=value）的时候，那么如果直接使用Collection就不能直接满足要求，可以使用Map接口实现此类数据的保存，并且Map接口还提供有根据key查找value的功能。

在Map接口里面定义有如下的常用方法。

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public V put(K key, V value) | 普通 | 向集合中保存数据 |
| 2 | public V get(Object key) | 普通 | 根据key查找对应的value数据 |
| 3 | public Set<Map.Entry<K, V>> entrySet() | 普通 | 将Map集合转化为Set集合 |
| 4 | public Set<K> keySet() | 普通 | 取出全部的key |

在Map接口下有两个常用子类：HashMap、Hashtable。

**范例：**观察HashMap的使用

```java
package com.alpha.demo;
import java.util.HashMap;
import java.util.Map;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = new HashMap<String, Integer>();
		map.put("壹", 1);
		map.put("贰", 2);
		map.put("叁", 3);
		map.put("叁", 33); // key数据重复
		System.out.println(map);
	}
}
```

通过以上的操作可以发现如下特点：

* 使用HashMap定义的Map集合是无序存放的（顺序无用）；
* 如果发现出现了重复的key会进行覆盖，使用新的内容替换掉旧的内容。

在Map接口里面提供有get()方法，这个方法的主要功能是根据key查找所需要的value。

**范例：**查询操作

```java
package com.alpha.demo;
import java.util.HashMap;
import java.util.Map;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = new HashMap<String, Integer>();
		map.put("壹", 1);
		map.put("贰", 2);
		map.put("叁", 3);
		map.put(null, 0); // key的内容为null
		System.out.println(map.get("壹"));
		System.out.println(map.get("陆")); // 如果key不存在，返回null
		System.out.println(map.get(null));
	}
}
```

通过以上的代码可以发现，Map存放数据的最终目的实际上就是为了信息的查找，但是Collection存放数据的目的是为了输出。

**范例：**取得全部的key

```java
package com.alpha.demo;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = new HashMap<String, Integer>();
		map.put("壹", 1);
		map.put("贰", 2);
		map.put("叁", 3);
		map.put(null, 0); // key的内容为null
		Set<String> set = map.keySet(); // 取得全部的key
		Iterator<String> iter = set.iterator();
		while (iter.hasNext()) {
			System.out.println(iter.next());
		}
	}
}
```

在Map接口下还有一个Hashtable的子类，此类是在JDK 1.0的时候提供的，属于最早的Map集合的实现操作，在JDK 1.2的时候让其多实现了一个Map接口，从而保存下来继续使用。

**范例：**使用Hashtable

```java
package com.alpha.demo;
import java.util.Hashtable;
import java.util.Map;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = new Hashtable<String, Integer>();
		map.put("壹", 1);
		map.put("贰", 2);
		map.put("叁", 3);
		map.put(null, 0); // key的内容为null
		System.out.println(map);
	}
}
```

发现Hashtable里面对于key和value的数据都不允许设置为null。

> 程序抛出的异常代码如下：
> 
> ```java
> Exception in thread "main" java.lang.NullPointerException
> 	at java.util.Hashtable.put(Hashtable.java:464)
> 	at com.alpha.demo.TestDemo.main(TestDemo.java:10)
> ```
> 
> 查阅JDK源码：
> 
> ```java
> public synchronized V put(K key, V value) {
>     if (value == null) {
>         throw new NullPointerException();
>     }
>     Entry<?,?> tab[] = table;
>     int hash = key.hashCode();
>     int index = (hash & 0x7FFFFFFF) % tab.length;
>     Entry<K,V> entry = (Entry<K,V>)tab[index];
>     for(; entry != null ; entry = entry.next) {
>         if ((entry.hash == hash) && entry.key.equals(key)) {
>             V old = entry.value;
>             entry.value = value;
>             return old;
>         }
>     }
>     addEntry(hash, key, value, index);
>     return null;
> }
> ```

**面试题：**请解释HashMap与Hashtable的区别？

| No. | 区别 | HashMap（90%） | Hashtable（10%） |
| :---: | :---: | :---: | :---: |
| 1 | 推出时间 | JDK 1.2时推出，属于新的类 | JDK 1.0时推出，属于旧的类 |
| 2 | 性能 | 采用异步处理 | 采用同步处理 |
| 3 | 数据安全 | 非线程安全 | 线程安全 |
| 4 | 设置null | 允许key或value内容为null | 不允许设置null |

而在实际的使用之中，遇见了Map接口基本上就使用HashMap类。

### 关于Iterator输出的问题（核心）

在之前强调过，只要是集合的输出一定要使用Iterator完成，但是在整个Map接口里面并没有定义任何的可以返回Iterator接口对象的方法，所以下面如果要想使用Iterator输出Map集合，首先必须要针对于Map集合与Collection集合保存数据的特点进行分析后才能实现。

每当用户使用put()方法向Map集合里面保存一对数据的时候，实际上所有的数据都会被自动的封装为Map.Entry接口对象。观察Map.Entry接口定义：

```java
public static interface Map.Entry<K,V>
```

在这个接口里面定义了两个操作：

* 取得key：public K getKey()；
* 取得value：public V getValue()；

![](/images/chapter-3/section-19/1.png)

在Map接口里面定义有一个将Map集合转化为Set集合的方法：public Set<Map.Entry<K, V>> entrySet()。

_**Map集合利用Iterator接口输出的步骤：**_

* _**利用Map接口的entrySet()方法将Map集合变为Set集合，里面的泛型是Map.Entry；**_
* _**利用Set集合中的iterator()方法将Set集合进行Iterator输出；**_
* _**每一次Iterator循环取出的都是Map.Entry接口对象，利用此对象进行key与value的取出。**_

**范例：**利用Iterator实现Map接口的输出

```java
package com.alpha.demo;
import java.util.Hashtable;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = new Hashtable<String, Integer>();
		map.put("壹", 1);
		map.put("贰", 2);
		map.put("叁", 3);
		// 将Map集合变为Set集合，目的是为了使用 iterator()方法
		Set<Map.Entry<String, Integer>> set = map.entrySet();
		Iterator<Map.Entry<String, Integer>> iter = set.iterator();
		while (iter.hasNext()) {
			Map.Entry<String, Integer> me = iter.next();
			System.out.println(me.getKey() + " = " + me.getValue());
		}
	}
}
```

以上的代码会不定期的出现，一定要掌握步骤。

### 关于Map集合中key的说明

在使用Map接口的时候可以发现，几乎可以使用任意的类型来作为key或value的存在，那么也就表示可以使用自定义的类型作为key。作为key的自定义的类必须要覆写Object类中的hashCode()与equals()两个方法。

在以后使用Map集合的时候，首选的key类型是String，尽量不要去使用其他类型。

### Stack子类（了解）

Stack表示的是栈操作，栈是一种先进后出的数据结构。而Stack是Vector的子类。

```java
public class Stack<E>
extends Vector<E>
```

虽然Stack是Vector子类，可是它不会使用Vector类的方法，它使用自己的方法：

* 入栈：public E push(E item)；
* 出栈：public E pop()；

**范例：**观察栈的操作

```java
package com.alpha.demo;
import java.util.Stack;
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

### Properties子类

Properties是Hashtable的子类，主要是进行属性的操作（属性的最大特点是利用字符串设置key和value）。观察Properties类的定义结构：

```java
public class Properties
extends Hashtable<Object,Object>
```

在使用Properties类的时候不需要设置泛型类型，因为从它一开始出现就只能够保存String。在Properties类里面主要使用如下的操作方法：

* 设置属性：public Object setProperty(String key, String value)；
* 取得属性：public String getProperty(String key)，如果key不存在返回null；
* 取得属性：public String getProperty(String key, String defaultValue)，如果key不存在返回defaultValue；

**范例：**属性的基本操作

```java
package com.alpha.demo;
import java.util.Properties;
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

在Properties类里面提供有数据的输出操作：public void store(OutputStream out, String comments) throws IOException。

**范例：**将属性信息保存在文件里

```java
package com.alpha.demo;
import java.io.FileOutputStream;
import java.util.Properties;
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

也可以从指定的输入流中读取属性信息：public void load(InputStream inStream) throws IOException；

**范例：**通过文件流读取属性内容

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

对于属性文件（资源文件）而言，除了可以使用Properties类读取之外，也可以使用ResourceBundle类读取。

### Collections工具类

在Java提供类库的时候考虑到用户的使用方便性，所以专门提供了一个集合的工具类 —— Collections，这个工具类可以实现List、Set、Map集合的操作。

* 为集合追加数据：public static <T> boolean addAll(Collection<? super T> c, T... elements)；
* 翻转集合：public static void reverse(List<?> list)；

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

**面试题：**请解释Collection与Collections的区别？

* Collection是集合操作的接口；
* Collections是集合操作的工具类，可以进行List、Set、Map集合的操作。

### 数据流：Stream

在JDK 1.8开始发现整个类集里面所提供的接口都出现了大量的default或者是static方法，以Collection的父借口Iterable接口里面定义的一个方法来观察：default void forEach(Consumer<? super T> action)。

**范例：**利用forEach()方法输出

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("Hello");
		all.add("World");
		all.add("Demo");
		// System.out.println(String str)
		all.forEach(System.out :: println);
	}
}
```

在日后的开发之中几乎不会使用到这种方式，因为forEach()只能够实现输出，但是很多时候我们需要在集合数据输出的同时还要进行数据的加工处理，主要使用的还是Iterator输出方式。

除了使用Iterator迭代取出数据并且处理之外，在JDK 1.8里面又提供了一个专门可以进行数据处理的类：java.util.stream.Stream，这个类的对象可以利用Collection接口提供的方法操作：default Stream<E> stream()。

**范例：**取得Stream对象

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("Hello");
		all.add("World");
		all.add("Demo");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		System.out.println(stream.count()); // 取得数据个数
	}
}
```

取得了Stream类对象就可以进行数据的加工操作。

**范例：**取消重复数据

* Stream类里面提供有一个消除重复的方法：public Stream<T> distinct()；
* 收集器：public <R, A> R collect(Collector<? super T, A, R> collector)；
  * Collectors类：public static <T> Collector<T, ?, List<T>> toList()；

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("Hello");
		all.add("Hello");
		all.add("World");
		all.add("World");
		all.add("World");
		all.add("Demo");
		all.add("Demo");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		//System.out.println(stream.distinct().count()); // 取得数据个数
		// 取出掉所有的重复数据后形成新的数据集合，里面不包含重复的内容
		List<String> newAll = stream.distinct().collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

Stream类是进行数据处理的，那么必然要涉及到数据的筛选（过滤），Stream里面支持有数据的筛选操作：public Stream<T> filter(Predicate<? super T> predicate)。

**范例：**数据过滤

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		List<String> newAll = stream.distinct().filter((x) -> x.contains("a")).collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

整个数据操作已经实现了过滤功能，但是发现这个时候的过滤是区分大小写的。那么就要在过滤之前对数据进行一些额外的处理，例如：统一将大写转为小写。在Stream类里面有专门的数据处理方法：pulbic <R> Stream<R> map(Function<? super T, ? extends R> mapper)。

**范例：**数据处理后过滤

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		List<String> newAll = stream.distinct().map((x) -> x.toLowerCase()).filter((x) -> x.contains("a")).collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

在Stream接口里面提供有进行集合数据分页的操作：

* 设置跳过的数据行数：public Stream<T> skip(long n)；
* 设置取出的数据个数：public Stream<T> limit(long maxSize)；

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		List<String> newAll = stream.distinct().map((x) -> x.toLowerCase()).skip(2).limit(2)
				.collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

在Stream接口里面还可以进行数据的全匹配或部分匹配：

* 全匹配：public boolean allMatch(Predicate<? super T> predicate)；
* 匹配任意一个：public boolean anyMatch(Predicate<? super T> predicate)；

**范例：**实现数据的匹配查询

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		if (stream.anyMatch((x) -> x.contains("Linux"))) {
			System.out.println("数据存在");
		}
	}
}
```

在实际之中有可能会出现多个匹配的条件，在断言型的函数式接口里面提供有如下的方法：

* 或操作：default Predicate<T> or(Predicate<? super T> other)；
* 与操作：default Predicate<T> and(Predicate<? super T> other)；

**范例：**设置多个条件

```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Predicate<String> p1 = (x) -> x.contains("Linux");
		Predicate<String> p2 = (x) -> x.contains("Windows");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		if (stream.anyMatch(p1.or(p2))) { // 同时使用两个条件
			System.out.println("数据存在");
		}
	}
}
```

利用这样的匹配条件，可以针对于数据进行方便的查询操作。

如果要想更好的反映出Stream的操作优势，必须结合MapReduce一起观察。

* 数据分析方法：public Optional<T> reduce(BinaryOperator<T> accumulator)；

**范例：**实现一个MapReduce

```java
class ShopCar {
	private String pname; // 商品名称
	private double price; // 商品单价
	private int amount; // 购买数量
	public ShopCar(String pname, double price, int amount) {
		this.pname = pname;
		this.price  = price;
		this.amount = amount;
	}
	public String getPname() {
		return pname;
	}
	public double getPrice() {
		return price;
	}
	public int getAmount() {
		return amount;
	}
}
```

此类设计的时候专门设计出了商品的单价与数量，这样如果要想取得某一个商品的花费就必须使用数量乘以单价。

**范例：**进行数据的保存于初步的处理

```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<ShopCar> all = new ArrayList<ShopCar>();
		all.add(new ShopCar("宾利	", 8000.0, 10));
		all.add(new ShopCar("宝马", 4500.0, 35));
		all.add(new ShopCar("布加迪威龙", 12000.0, 8));
		all.stream().map((x) -> x.getAmount() * x.getPrice()).forEach(System.out::println);
	}
}
```

此时已经对每一个数据进行了处理。但是这个时候没有总价。于是要对处理后的数据进行统计操作，使用reduce()完成。

**范例：**统计处理数据

```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<ShopCar> all = new ArrayList<ShopCar>();
		all.add(new ShopCar("宾利	", 8000.0, 10));
		all.add(new ShopCar("宝马", 4500.0, 35));
		all.add(new ShopCar("布加迪威龙", 12000.0, 8));
		double s = all.stream().map((x) -> x.getAmount() * x.getPrice()).reduce((sum, m) -> sum + m).get();
		System.out.println("花费总金额：" + s);
	}
}
```

以上只是实现了一个最简单的MapReduce，但是所完成的统计功能实在是过于有限，如果要想实现更能完善的统计操作，需要使用Stream接口里面定义的一下方法：

* 按照Double处理：public DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper)；
* 按照Integer处理：public IntStream mapToInt(ToIntFunction<? super T> mapper)；
* 按照Long处理：public LongStream mapToLong(ToLongFunction<? super T> mapper)；

**范例：**实现数据的统计操作

```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<ShopCar> all = new ArrayList<ShopCar>();
		all.add(new ShopCar("宾利	", 8000.0, 10));
		all.add(new ShopCar("宝马", 4500.0, 35));
		all.add(new ShopCar("布加迪威龙", 12000.0, 8));
		DoubleSummaryStatistics dss = all.stream().mapToDouble((sc) -> sc.getAmount() * sc.getPrice()).summaryStatistics();
		System.out.println("商品个数：" + dss.getCount());
		System.out.println("平均花费：" + dss.getAverage());
		System.out.println("最高花费：" + dss.getMax());
		System.out.println("最低花费：" + dss.getMin());
		System.out.println("总花费：" + dss.getSum());
	}
}
```

这种代码是在是过于麻烦，但是他的确是简化了代码的编写。


