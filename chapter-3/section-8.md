## 比较器

---

1、重新认识一下Arrays类；

2、两种比较器的使用；

3、数据结构 —— 二叉树（Binary Tree）

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

### 比较器：Comparable（核心）

观察Arrays类中提供的数组排序方法：

* 对象数组排序：public static void sort(Object[] a)。

发现Arrays类里面可以直接利用sort()方法实现对象数组排序。

**范例：**编写测试代码

```java
package com.alpha;
import java.util.Arrays;
class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]";
	}
}
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Book[] books = new Book[] {
				new Book("C++", 15.6),
				new Book("Java", 29.8),
				new Book("Python", 9.9),
				new Book("C#", 35.6)
		};
		Arrays.sort(books); // 对象数组排序
		System.out.println(Arrays.toString(books));
	}
}
```

```java
Exception in thread "main" java.lang.ClassCastException: com.alpha.Book cannot be cast to java.lang.Comparable
	at java.util.ComparableTimSort.countRunAndMakeAscending(Unknown Source)
	at java.util.ComparableTimSort.sort(Unknown Source)
	at java.util.Arrays.sort(Unknown Source)
	at com.alpha.MainClass.main(MainClass.java:25)
```

造成此类异常只有一个原因：两个没有关系的类对象发生了强制性的转换。

每一个对象实际上只保留有地址信息，地址里面是有内容的，所以如果是普通的int型数组要进行比较，只要判断大小就够了，但是如果是对象数组，里面包含的只是编码（地址），这样的比较是没有意义的。所以对于对象来说，必须设置明确的比较规则。

比较的规则就是由Comparable接口定义的，此接口定义如下：

```java
public interface Comparable<T> {
	public int compareTo(T o);
}
```

实际上Stirng类就是Comparable接口的子类，之前使用的compareTo()方法就是比较的操作功能，如果现在用户要针对于对象进行比较，建议compareTo()方法返回三类数据：1（大于）、0（等于）、-1（小于）。

**范例：**使用比较器

```java
package com.alpha;
import java.util.Arrays;
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
	public int compareTo(Book o) { // Arrays.sort()会自动调用此方法
		if (this.price > o.price) {
			return 1;
		} else if (this.price < o.price) {
			return -1;
		}
		return 0;
	}
}
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Book[] books = new Book[] {
				new Book("C++", 15.6),
				new Book("Java", 29.8),
				new Book("Python", 9.9),
				new Book("C#", 35.6)
		};
		Arrays.sort(books); // 对象数组排序
		System.out.println(Arrays.toString(books));
	}
}
```

compareTo()方法由Arrays.sort()自动进行调用。

_总结：以后不管何种状况下，只要是一组对象要排序，对象所在的类一定要实现Comparable接口。_

### 数据结构 —— BinaryTree（了解）

树是一种比链表更为复杂的概念应用，本质也属于动态数组对象，但是与链表不同在于，树的最大特征是可以针对于数据进行排序。

树的操作原理：选择第一个数据作为根节点，而后比根节点小的放在根节点的左子树，比根节点大的数据放在右子树，取得的时候按照终须遍历的方式取出（左-中-右）。

在任何数据结构里面Node类的核心功能是保存真实数据以及配置节点关系。

**范例：**实现二叉树

* 定义出要使用的数据，数据所在的类要实现Comparable接口；

```java
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
	public int compareTo(Book o) { // Arrays.sort()会自动调用此方法
		if (this.price > o.price) {
			return 1;
		} else if (this.price < o.price) {
			return -1;
		}
		return 0;
	}
}
```

* 定义二叉树，所有的数据机构都需要有Node类的支持。

```java
@SuppressWarnings("rawtypes")
class BinaryTree {
	private class Node {
		private Comparable data; // 排序的依据就是Comparable
		private Node left; // 左子树
		private Node rigth; // 右子树
		public Node(Comparable data) {
			this.data = data;
		}
		@SuppressWarnings("unchecked")
		public void addNode(Node newNode) {
			if (this.data.compareTo(newNode.data) > 0) {
				if (this.left == null) {
					this.left = newNode;
				} else {
					this.left.addNode(newNode);
				}
			} else {
				if (this.rigth == null) {
					this.rigth = newNode;
				} else {
					this.rigth.addNode(newNode);
				}
			}
		}
		public void toArrayNode() {
			if (this.left != null) { // 表示有左子树
				this.left.toArrayNode();
			}
			BinaryTree.this.retData[BinaryTree.this.foot ++] = this.data;
			if (this.rigth != null) { // 表示有右子树
				this.rigth.toArrayNode();
			}
		}
	}
	private Node root; // 定义根节点
	private int count; // 保存元素个数
	private Object[] retData;
	private int foot;
	public void add(Object obj) { // 进行数据的追加 
		Comparable com = (Comparable) obj; // 必须变为Comparable才可以实现Node
		Node newNode = new Node(com); // 创建新的节点
		if (this.root == null) { // 现在不存在根节点
			this.root = newNode; // 保存根节点
		} else {
			this.root.addNode(newNode); // 交给Node类处理
		}
		this.count ++;
	}
	public Object[] toArray() {
		if (this.root == null) {
			return null;
		}
		this.foot = 0;
		this.retData = new Object[this.count];
		this.root.toArrayNode();
		return this.retData;
	}
}
```

* 测试：

```java
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		BinaryTree bt = new BinaryTree();
		bt.add(new Book("C++", 15.6));
		bt.add(new Book("Java", 29.8));
		bt.add(new Book("Python", 9.9));
		bt.add(new Book("C#", 35.6));
		Object[] objs = bt.toArray();
		System.out.println(Arrays.toString(objs));
	}
}
```

这些内容在Java的类库中都有自己的实现。

### 挽救的比较器：Comparator

Comparable接口的主要特征是在类定义的时候就默认实现好的接口，那么如果说现在有一个类已经开发完善了。

```java
class Book{
	private String title;
	private double price;
	public Book() {}
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	public double getPrice() {
		return price;
	}
	public void setPrice(double price) {
		this.price = price;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]\n";
	}
}
```

但是由于初期的设计并没有安排此类对象数组的排序。而后又突然需要实现对象数组的排序，那么这个时候在不能修改Book类定义的情况下是不可能使用Comparable接口的。为此，在Java里面为了解决这样的问题，又出现了另外一个比较器：java.util.Comparator，原本在Compartator接口下有两个方法：

```java
@FunctionalInterface
public interface Comparator<T> {
	public int compare(T o1, T o2);
	boolean equals(Object obj);
}
```

而真正要实现的只有compare()方法，需要单独准备出一个类来实现Comparator接口，这个类将作为指定类的排序类。

**范例：**定义排序的工具类

```java
class BookComparator implements Comparator<Book> {
	@Override
	public int compare(Book o1, Book o2) {
		if (o1.getPrice() > o2.getPrice()) {
			return 1;
		} else if (o1.getPrice() < o2.getPrice()) {
			return -1;
		}
		return 0;
	}
}
```

之前使用Comparable接口的时候利用的是Arrays类中的sort()方法，可是现在更换了一个接口之后，那么也可以使用另一个被重载的sort()方法：public static <T> void sort(T[] a, Comparator<? super T> c)。

**范例：**实现排序

```java
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Book[] books = new Book[] {
				new Book("C++", 15.6),
				new Book("Java", 29.8),
				new Book("Python", 9.9),
				new Book("C#", 35.6)
		};
		Arrays.sort(books, new BookComparator());
		System.out.println(Arrays.toString(books));
	}
}
```

使用Comparator比较麻烦，因为要定义一个专门的比较器，而且调用排序的时候也要明确的指明一个排序规则类。

**面试题：**请解释Comparable和Comparator的区别？（**面试题：**请解释两种比较器的区别？）

* 如果对象数组要进行排序那么必须设置排序规则，可以使用Comparable或Comparator实现；
* java.lang.Comparable是在一个类定义的时候实现好的接口，这样本类的对象数组就可以进行排序，在Comparable接口下定义有一个public int compareTo()方法；
* java.util.Comparable是专门定义一个指定类的比较规则，属于挽救的比较操作，里面有两个方法：public int compare()、public boolean equals()。

#### 总结

1、以后不管何种情况，只要牵扯到对象数组的排序一定使用Comparable接口。

2、熟练编写以及理解二叉树。

