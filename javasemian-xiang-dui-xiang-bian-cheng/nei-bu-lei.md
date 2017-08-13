## 内部类

---

内部类的基本定义结构

* 使用static定义内部类
* 在方法中定义内部类

从开发的角度，内部类能少用就少用。优先考虑的还是普通类。

### 基本概念

所谓的内部类指的就是在一个类的内部继续定义了其他内部结构类的情况。

**范例：**观察内部类的基本形式

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	class Inner { // 定义了一个内部类
		public void print() {
			System.out.println(msg);
		}
	}
	public void fun() {
		// 实例化内部类对象，并且调用print()方法
		new Inner().print();
	}
}
public class MainClass {
	public static void main(String[] args) {
		Outer out = new Outer(); // 实例化外部类对象
		out.fun(); // 调用外部类方法
	}
}
```

以上类的代码并不麻烦，但是从结构上讲是存在问题的，因为从类的结构本身就意味着属性与方法的组成，可是一个类里面又定义了另外一个类。这个时候内部类牺牲了类的结构，可是它实现了一个很重要的目的。

为了观察内部类达到的目的，下面将之前的内部类拿到外面来，并且要求实现与之完全一样的功能。

**范例：**将内部类取出

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	public void fun() {
		// 实例化内部类对象，并且调用print()方法
		new Inner().print();
	}
}
class Inner { // 定义了一个内部类
	public void print() {
		System.out.println(msg);
	}
}
public class MainClass {
	public static void main(String[] args) {
		Outer out = new Outer(); // 实例化外部类对象
		out.fun(); // 调用外部类方法
	}
}
```

1、内部类需要访问msg属性，但是此属性属于Outer类中的，而在Outer类里面此属性使用private进行封装，所以此时如果要得到这个属性的内容，需要定义一个getter方法。

```java
public String getMsg() {
	return this.msg;
}
```

2、此时Outer类的getMsg()是一个普通方法，那么所有类中的普通方法必须要通过类的实例化对象进行调用。

此时可以创建一个新的Outer对象，但是这并不满足之前所规定的条件，那么现在最好的做法是将主方法之中的Outer类对象传递给Inner类。

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	public String getMsg() {
		return this.msg;
	}
	public void fun() {
		// 实例化内部类对象，并且调用print()方法
		new Inner(this).print();
	}
}
class Inner { // 定义了一个内部类
	private Outer out;
	public Inner(Outer out) {
		this.out = out;
	}
	public void print() {
		System.out.println(this.out.getMsg());
	}
}
public class MainClass { 
	public static void main(String[] args) {
		Outer out = new Outer(); // 实例化外部类对象
		out.fun(); // 调用外部类方法
	}
}
```

实际上以上代码的最终目的只有一个：让内部类可以访问外部类中定义的一个私有的msg属性内容。

内部类有一个最大的优点：可以方便的访问外部类的私有属性。

但是需要注意的是内部类可以方便的访问外部类的私有属性，同样，外部类也可以通过内部类对象访问内部类的私有属性。

**范例：**访问内部类的私有属性

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	class Inner { // 定义了一个内部类
		private String info = "你好，世界！";
		public void print() {
			System.out.println(msg);
		}
	}
	public void fun() {
		Inner in  = new Inner(); // 内部类对象
		// 直接利用内部类对象访问内部类定义的私有属性
		System.out.println(in.info);
	}
}
public class MainClass {
	public static void main(String[] args) {
		Outer out = new Outer(); // 实例化外部类对象
		out.fun(); // 调用外部类方法
	}
}
```

一旦使用了内部类之后，私有属性的访问就变得非常简单了。

在之前的案例里一直要求访问类属性的时候一定要在前面加上this，但是如果直接在pring()方法里面加上this表示的是查找本类的属性，但是此时要访问的实际上是我外部类的属性，那么应该使用“外部类.this”。

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	class Inner { // 定义了一个内部类
		public void print() {
			// 外部类.this = 外部类的当前对象
			System.out.println(Outer.this.msg);
		}
	}
	public void fun() {
		new Inner().print();
	}
}
public class MainClass {
	public static void main(String[] args) {
		Outer out = new Outer(); // 实例化外部类对象
		out.fun(); // 调用外部类方法
	}
}
```

以上的所有代码都有一个特点：通过外部类的一个fun()方法访问内部类的操作。内部类能不能像普通对象那样直接在外部产生实例化对象调用？

要想解决这个问题，那么就要通过内部类编译后生成的文件形式来观察。发现内部类的class文件的形式是：Outer$Inner.class。所有的“$”是在文件中的命名，如果换回到了程序里面就变为了“.”，也就是说内部类的名称就是“外部类.内部类”。此时可以给出内部类对象的实例化语法：

```java
外部类.内部类 内部类对象 = new 外部类().new 内部类();
```

**范例：**实例化内部类对象

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	class Inner { // 定义了一个内部类
		public void print() {
			// 外部类.this = 外部类的当前对象
			System.out.println(Outer.this.msg);
		}
	}
}
public class MainClass {
	public static void main(String[] args) {
		Outer.Inner in = new Outer().new Inner();
		in.print();
	}
}
```

内部类不可能离开外部类的实例化对象，所以一定要先实例化外部类对象之后才可以使用内部类对象。如果真的使用到了内部类，也基本上不会像以上的操作那样进行的。一定是通过外部类进行访问内部类。

如果现在一个内部类只希望被一个外部类访问，不能够被外部调用，那么可以使用private进行定义。

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	private class Inner { // 定义了一个内部类
		public void print() {
			// 外部类.this = 外部类的当前对象
			System.out.println(Outer.this.msg);
		}
	}
}
```

此时的内部类是不可能在外部进行对象实例化的。

