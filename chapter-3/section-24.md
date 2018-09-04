## List集合

### List接口

List子接口是Collection子接口中最为常用（80%）的一个子接口，其最大的特点是允许保存有重复元素数据，该接口的定义如下：
```java
public interface List<E> extends Collection<E>
```

![](/images/chapter-3/Collection/List.png)

但是这个接口对Collection接口进行了一些功能的扩充。在List子接口里面重点掌握以下方法的使用：

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public E get(int index) | 普通 | 取得索引编号的内容 |
| 2 | public E set(int index, E element) | 普通 | 修改指定索引编号的内容 |
| 3 | public ListIterator<E> listIterator() | 普通 | 为ListIterator接口实例化 |

而List本身是属于接口，所以如果要想使用此接口进行操作，那么就必须存在有子类，可以使用ArrayList子类实现操作（还有另一个Vector子类，90%的情况下选择的是ArrayList）。

### ArrayList

ArrayList类是List接口最为常用的一个子类。下面将利用此类来验证之前所描述过的操作方法。

![](/images/chapter-3/Collection/ArrayList.png)

**范例：** List基本操作

```java
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

**范例：** 为Collection接口实例化

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

**范例：** 在集合里面保存对象
```java
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

通过对 `ArrayList` 类名的理解就已经可以知道， `ArrayList` 内部封装着一个数组来保存所有的信息，为了深入了解 `ArrayList` 的内部结构，我们可以对其部分源码进行分析。

```java
class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    /**
     * 默认容量
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * 构造函数初始化个数为 0 时的空数组
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
     * 默认容量空元素数据
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
     * 内部维护的数组
     */
    transient Object[] elementData; // non-private to simplify nested class access;

    /**
     * 根据传入的初始化个数生成内部维护数组
     * @param initialCapacity 传入的初始化参数
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            // 大于 0 时按传入个数初始化数组
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            // 传入 0 时赋予空数组
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            // 其他值抛出参数错误异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                    initialCapacity);
        }
    }

    /**
     * 默认构造函数
     */
    public ArrayList() {
        // 赋予默认空数组
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    /**
     * 返回至少与给定最小容量一样大的容量。
     * 如果满足，则返回当前容量增加50％。
     * 除非给定的最小容量大于MAX_ARRAY_SIZE，
     * 否则不会返回大于MAX_ARRAY_SIZE的容量。
     *
     * @param minCapacity 所需的最小容量
     * @return 新数组的容量
     * @throws OutOfMemoryError 如果最小容量小于零
     */
    private int newCapacity(int minCapacity) {
        // 当前容量
        int oldCapacity = elementData.length;
        // 新容量 = 当前容量 + 当前容量的一半
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        // 新容量 - 所需的最小容量 <= 0
        if (newCapacity - minCapacity <= 0) {
            // 如果当前数组是默认的空数组，返回默认容量与所需的最小容量中较大的一个
            if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
                return Math.max(DEFAULT_CAPACITY, minCapacity);
            // 如果所需的最小容量小于零，抛出内存溢出错误
            if (minCapacity < 0) 
                throw new OutOfMemoryError();
            return minCapacity;
        }
        return (newCapacity - MAX_ARRAY_SIZE <= 0)
                ? newCapacity
                : hugeCapacity(minCapacity);
    }
}
```

通过对源码进行分析可以看出， `ArrayList` 里面所包含的数据实际上就是一个对象数组。如果在进行数据追加的时候发现 `ArrayList` 集合里面保存的对象数组的长度不够的时候会进行新的数组开辟，同时将原始的旧内容拷贝到新数组中。、

如果在实例化 `ArrayList` 类对象的时候没有传入初始化的长度，则默认情况下会使用一个空数组，但是如果在进行追加元素的时候发现数据不够用了，则会判断当前的增长容量与默认的容量的大小，使用较大的一个进行数据的开辟，所以可以得出一个结论。
* JDK1.9之前：`ArrayList`默认的构造只会使用默认的空数组，使用的时候才会开辟数组，默认的开辟长度为10；
* JDK1.9之后：`ArrayList`默认的构造实际上就会默认开辟大小为10的数组；

当 `ArrayList` 之中保存的容量不足的时候会扩展至原容量的1.5倍，所以在使用 `ArrayList` 的时候要估算出大概的存入数量，避免多次扩容产生较多的垃圾数组。

### LinkedList

在 `List` 接口里面还有另一个比较常用的子类： `LinkedList` ，这个类与之前的 `ArrayList` 一样，通过名字就可以推断出其内部是基于链表的实现。

![](/images/chapter-3/Collection/LinkedList.png)

`LinkedList` 的操作与 `ArrayList` 完全一致，只是其内部的实现机制不同，下面通过分析 `LinkedList` 的部分源码来了解其内部机制。

```java
public class LinkedList<E>
        extends AbstractSequentialList<E>
        implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    /**
     * 链表的头结点
     */
    transient Node<E> first;

    /**
     * 链表的尾节点
     */
    transient Node<E> last;

    /**
     * 构造一个空列表
     */
    public LinkedList() {
    }

    /**
     * LinkedList内部维护着一个链表
     * @param <E>
     */
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }

    /**
     * 添加时调用 {@code linkLast}
     * @param e
     * @return
     */
    public boolean add(E e) {
        linkLast(e);
        return true;
    }

    /**
     * 将添加的数据封装为 Node 保存到链表的末尾
     * @param e
     */
    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
}
```

可以发现 `LinkedList` 内部实质上就是封装了一个双向链表的实现。

**面试题：**`ArrayList`与`LinkedList`的区别？
* `ArrayList`是数组实现的集合操作，`LinkedList`是链表实现的集合操作；
*  使用`get()`方法获得数据时，`ArrayList`的时间复杂度为`O(1)`，`LinkedList`的时间复杂度为`O(n)`；
* `ArrayList`在添加元素数量到一定界限时需要对数组容量进行扩容，`LinkedList`没有任何影响。

### Vector

在最早JDK 1.0的时候就已经提供有Vector类，并且这个类被大量的使用。但是到了JDK 1.2的时候由于类集框架的引入，所以对于整个集合的操作就有了新的标准，那么为了可以继续保留下Vector类，所以让这个类多实现了一个List接口。

![](/images/chapter-3/Collection/Vector.png)

**范例：** 使用Vector

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

_由于`Vector`的源码与`ArrayList`的源码大致相同，不再进行单独的分析。_

**面试题：**请解释`ArrayList`与`Vector`的区别？

| No. | 区别 | ArrayList（90%） | Vector（10%） |
| :---: | :---: | :---: | :---: |
| 1 | 推出时间 | JDK 1.2时推出，属于新的类 | JDK 1.0时推出，属于旧的类 |
| 2 | 性能 | 采用异步处理 | 采用同步处理 |
| 3 | 数据安全 | 非线程安全 | 线程安全 |
| 4 | 输出 | Iterator、ListIterator、foreach | Iterator、ListIterator、foreach、Enumeration |

在以后的开发之中，如果使用了List子接口就使用`ArrayList`子接口。
