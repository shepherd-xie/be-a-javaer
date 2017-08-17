## 数组操作

---

为了巩固继承与方法覆写的概念，要求定义一个数组操作类（Array类），在这个类里面可以进行数组的操作，由外部传入数组的大小，并且要求实现数据的保存以及数据的输出，还要在这个类上派生出两个子类：

* 排序类：通过此类取得的数据可以进行排序；
* 反转类：通过此类取得数组数据要求采用倒序的方式进行输出。

### 开发数组的父类

**范例：**基础实现

```java
class Array {
	private int data[]; // 定义一个数组
	private int foot = -1; // 表示数组的操作索引
	public Array(int len) { // 构造本类是需要传递长度
		if (len > 0) {
			this.data = new int[len]; // 开辟一个数组
		} else {
			this.data = new int[1]; // 维持一个元素的大小
		}
		this.foot = 0;
	} 
	public boolean add(int num) {
		if (this.foot < this.data.length) {
			this.data[this.foot ++] = num; // 保存数据
			return true; // 保存成功
		}
		return false;
	}
	public int[] getData() { // 取得数组内容
		return this.data;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Array arr = new Array(3);
		System.out.println(arr.add(10));
		System.out.println(arr.add(20));
		System.out.println(arr.add(30));
		System.out.println(arr.add(100));
		System.out.println(arr.add(200));
		int[] temp = arr.getData();
		for (int i = 0; i < temp.length; i ++) {
			System.out.println(temp[i]);
		}
	}
}
```

在以上的代码之中发现，此时Array类没有提供无参构造，因为必须要传入一个数组的大小，否则该类无法使用，并且Array类规定了要想增加数据使用add\(\)方法，要想取出数据使用getData\(\)方法。

### 开发排序类

所谓排序的实现就是在返回数据时对数据进行排序即可。

```java
class Array {
	private int data[]; // 定义一个数组
	private int foot = -1; // 表示数组的操作索引
	public Array(int len) { // 构造本类是需要传递长度
		if (len > 0) {
			this.data = new int[len]; // 开辟一个数组
		} else {
			this.data = new int[1]; // 维持一个元素的大小
		}
		this.foot = 0;
	} 
	public boolean add(int num) {
		if (this.foot < this.data.length) {
			this.data[this.foot ++] = num; // 保存数据
			return true; // 保存成功
		}
		return false;
	}
	public int[] getData() { // 取得数组内容
		return this.data;
	}
}
// 定义一个排序数组的子类
class SortArray extends Array {
	// Array类里面没有无参构造方法
	public SortArray(int len) {
		super(len); // 明确调用父类的有参构造
	}
	// 重写父类的getData()方法
	public int[] getData() {
		java.util.Arrays.sort(super.getData());
		return super.getData();
	}
}
public class MainClass {
	public static void main(String[] args) {
		SortArray arr = new SortArray(3);
		System.out.println(arr.add(100));
		System.out.println(arr.add(20));
		System.out.println(arr.add(3));
		System.out.println(arr.add(100));
		System.out.println(arr.add(200));
		int[] temp = arr.getData();
		for (int i = 0; i < temp.length; i ++) {
			System.out.println(temp[i]);
		}
	}
}
```

添加子类的思路：

* 不改变客户端已有的使用方法；
* 子类为了维持方法的功能重写了父类的方法。

### 开发反转类

反转类的要去也是保证客户端的操作不改变，也就是说父类的方法名称应该继续保留。

**范例：**开发反转类

```java
class Array {
	private int data[]; // 定义一个数组
	private int foot = -1; // 表示数组的操作索引
	public Array(int len) { // 构造本类是需要传递长度
		if (len > 0) {
			this.data = new int[len]; // 开辟一个数组
		} else {
			this.data = new int[1]; // 维持一个元素的大小
		}
		this.foot = 0;
	} 
	public boolean add(int num) {
		if (this.foot < this.data.length) {
			this.data[this.foot ++] = num; // 保存数据
			return true; // 保存成功
		}
		return false;
	}
	public int[] getData() { // 取得数组内容
		return this.data;
	}
}
// 定义一个排序数组的子类
class SortArray extends Array {
	// Array类里面没有无参构造方法
	public SortArray(int len) {
		super(len); // 明确调用父类的有参构造
	}
	// 重写父类的getData()方法
	public int[] getData() {
		java.util.Arrays.sort(super.getData());
		return super.getData();
	}
}
class ReverseArray extends Array {
	public ReverseArray(int len) {
		super(len);
	}
	public int[] getData() {
		int center = super.getData().length / 2;
		int head = 0;
		int tail = super.getData().length - 1;
		for (int x = 0; x < center; x ++) {
			int temp = super.getData()[head];
			super.getData()[head] = super.getData()[tail];
			super.getData()[tail] = temp;
			head ++;
			tail --;
		}
		return super.getData();
	}
}
public class MainClass {
	public static void main(String[] args) {
		ReverseArray arr = new ReverseArray(3);
		System.out.println(arr.add(100));
		System.out.println(arr.add(20));
		System.out.println(arr.add(3));
		System.out.println(arr.add(100));
		System.out.println(arr.add(200));
		int[] temp = arr.getData();
		for (int i = 0; i < temp.length; i ++) {
			System.out.println(temp[i]);
		}
	}
}
```

#### 总结

1、子类的功能就是根据特殊情况进行父类功能的扩充

2、子类扩充方法的时候尽量以父类定义的方法名称为主，可以根据实际情况进行方法的覆写。

