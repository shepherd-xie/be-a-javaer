## 代码块

---

主要观察普通代码快、构造块、静态块的主要作用。

在程序编写之中可以直接使用“{}”定义一段语句，根据此部分定义的位置以及声明的关键字的不同，代码块一共可以分为四种：普通代码块、构造块、静态块、同步代码块（在多线程的时候讲解）。

_**尽可能在写代码的过程中不要使用代码块。**_

### 普通代码块

如果说一个代码块写在了方法里面，那么就称它为普通代码块。

**范例：** 观察如下代码

```java
public class MainClass {
	public static void main(String[] args) {
		if (true) { // 条件一定满足
			int num = 10; // 局部变量
			System.out.println(num);
		}
		int num = 100; // 全局变量
		System.out.println(num);
	}
}
```

如果现在取消了if语句：

```java
public class MainClass {
	public static void main(String[] args) {
		{ // 普通代码块
			int num = 10; // 局部变量
			System.out.println(num);
		}
		int num = 100; // 全局变量
		System.out.println(num);
	}
}
```

实际上普通代码块就是为了防止在方法里面编写代码过多时有可能产生的变量重名，对一个方法的代码进行局部的分割。不过在实际开发之中，一个方法中的代码不会太长。

### 构造块

如果说现在将一个代码块写在了一个类里面，那么这个代码块就成为构造块。

**范例：** 定义构造块

```java
class Book {
	public Book() { // 构造方法
		System.out.println("【A】Book类的构造方法");
	}
	{ // 将代码块写在了类里面
		System.out.println("【B】Book类中的构造块");
	}
}
public class MainClass {
	public static void main(String[] args) {
		new Book();
	}
}
```

此时实例化Book类对象时，虽然执行的是Book类的构造方法，但是最终的结果发现，构造块也被执行了，而且构造块的调用优先于构造方法执行。

如果现在产生了多个Book类的对象，那么构造块将会被重复执行多次。

### 静态块

如果说一个代码块使用了static进行定义的话，那么就称为静态块，可是静态块的使用需要分为两种情况。

**情况一：**在非主类中使用

```java
class Book {
	public Book() { // 构造方法
		System.out.println("【A】Book类的构造方法");
	}
	{ // 将代码块写在了类里面
		System.out.println("【B】Book类中的构造块");
	}
	static {
		System.out.println("【C】Book类中的静态块");
	}
}
public class MainClass {
	public static void main(String[] args) {
		new Book();
		new Book();
		new Book();
	}
}
```

现在可以发现，静态块将优先于构造块执行，而且不管有多少个实例化对象，静态块只执行一次。它的主要功能是为了类中的static属性初始化。

```java
class Book {
	static String msg;
	public Book() { // 构造方法
		System.out.println("【A】Book类的构造方法");
	}
	{ // 将代码块写在了类里面
		System.out.println("【B】Book类中的构造块");
	}
	static {
		msg = "Hello";
		System.out.println("【C】Book类中的静态块");
	}
}
public class MainClass {
	public static void main(String[] args) {
		new Book();
		new Book();
		new Book();
		System.out.println(Book.msg);
	}
}
```

**情况二：**在主类中定义

```java
public class MainClass {
	static {
		System.out.println("********************");
	}
	public static void main(String[] args) {
		System.out.println("Hello World !");
	}
}
```

此时静态块将优先于主方法执行。

#### 总结

代码块在开发之中能不用就不用。
