## 辅助概念

* [final关键字](/chapter-2/section-17.md#final关键字)
* [多态性](/chapter-2/section-17.md#多态性)

---

### final关键字

在Java之中final称为终结器，在Java里面可以使用final定义类、方法和属性。

1、使用final定义的类不能够再有子类

**范例：**观察final定义的类

```java
final class A {}
class B extends A {}
```

一般在进行系统类开发的时候会在使用到final，如果要进行一些架构代码的开发，也会用到final，如果你只是一个使用者，很少会见到final定义的类。

2、使用final定义的方法不能被子类所覆写

在一些时候由于父类中的某些方法具备某些隐藏的属性，那么子类也必须使用此方法的时候，就加上final。

```java
class A {
	public final void fun() {}
}
class B extends A {
	public void fun() {}
}
```

以上的两种final的使用对于平常开发而言意义不大。

3、使用final定义的变量就成为了常量，常量必须在定义的时候设置好内容，并且不能修改

**范例：**定义常量

```java
class A {
	final double GOOD = 100.0;
	public final void fun() {
		GOOD = 1.1; // 不能够修改常量
	}
}
```

使用常量可以利用一些变量名称来描述一些数值。

为了让程序中的常量可以与变量进行有效的区分，所有的常量都要求使用大写字母的方式表示。

特别强调 —— 全局变量：public static final 声明的就是全局常量。

```java
public static void String msg = "Hello";
```

static的数据保存在公共数据去，所以此处的常量就是一个公共常量。

### 多态性

多态性严格来讲只能为其讲解基本概念，以及相关的使用限制。

多态性的依赖：方法的覆写。

以下的内容只是为了解释多态性的特点，但是代码本身没有任何的意义。

**范例：**观察如下的一个程序

```java
class A {
	public void print() {
		System.out.println("A、public void print()");
	}
}
class B extends A {
	public void print() {
		System.out.println("B、public void print()");
	}
}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.print();
	}
}
```

多态性严格来讲有两种描述形式：

* 方法的多态性：
  * 方法的重载：同一个方法名称，会根据传入参数的类型及个数不同执行不同的方法体；
  * 方法的覆写：同一个方法，会根据子类的不同，实现不同的功能。
* 对象的多态性：指的是发生在继承关系之中，子类和父类之间的转换；
  * 向上转型（自动完成）：父类 父类对象 = 子类实例；
  * 向下转型（强制完成）：子类 子类对象 = （子类）父类实例；

**范例：**向上转型

```java
class A {
	public void print() {
		System.out.println("A、public void print()");
	}
}
class B extends A {
	public void print() {
		System.out.println("B、public void print()");
	}
}
public class MainClass {
	public static void main(String[] args) {
		A a = new B();
		a.print();
	}
}
```

**范例：**向下转型

```java
class A {
	public void print() {
		System.out.println("A、public void print()");
	}
}
class B extends A {
	public void print() {
		System.out.println("B、public void print()");
	}
}
public class MainClass {
	public static void main(String[] args) {
		A a = new B(); // 向上转型
		B b = (B) a; // 向下转型
		a.print();
	}
}
```

* 向上转型：由于所有的子类对象实例都可以自动的向上转型，所以可以用于参数的统一。

```java
class A {
	public void print() {
		System.out.println("A、public void print()");
	}
}
class B extends A {
	public void print() {
		System.out.println("B、public void print()");
	}
}
class C extends A {
	public void print() {
		System.out.println("C、public void print()");
	}
}
public class MainClass {
	public static void main(String[] args) {
		// 只要是A类的子类都可以使用向上转型，由A类接收
		A a1 = new B(); // 向上转型
		A a2 = new C(); // 向上转型
		a1.print();
		a2.print();
	}
}
```

参数统一之后，还可以调用子类覆写后的方法体，即：同一个方法针对于不同的子类可以有不同的实现。

* 向下转型：指的是父类要调用子类自己的方法，所有的父类发生向上转型后只能够调用本类的方法，子类扩充的方法对本类而言是隐藏的。此时如果想要调用子类方法，就需要向下转型，将父类对象转为子类对象。

```java
class A {
	public void print() {
		System.out.println("A、public void print()");
	}
}
class B extends A {
	public void print() {
		System.out.println("B、public void print()");
	}
	public void fun() {
		System.out.println("B、public void fun()");
	}
}
public class MainClass {
	public static void main(String[] args) {
		A a = new B();
		B b = (B) a;
		b.fun();
	}
}
```

**疑问？××以上的代码如果不适用向上转型，而直接使用子类对象进行实例化也可以直接调用fun\(\)方法，问什么还需要转型？

在之前一直强调过，对于数据的操作分为两步：

* 第一步：设置数据（保存数据），最需要的是参数统一功能；
* 第二步：取出数据：

```java
public class MainClass {
	public static void main(String[] args) {
		function(new B()); // 向上转型
	}
	public static void function(A a) { // 统一参数
		// 调用个性化的特征
		B b = (B) a; // 因为要调用子类的特殊功能 
		b.fun();
	}
}
```

**对于对象的转型，给出以下的经验总结：**

* 80%的情况下都只会使用向上转型，因为可以得到参数类型的统一，方便与我们的程序设计；
  * 子类定义的方法大部分情况下请以父类的方法名称为标准进行覆写，不要过多的扩充方法；
* 5%的情况下会使用向下转型，目的是调用子类的特殊方法；
* 15%的情况下是不转型的。

在标准化开发之中向下转型应该尽量少出现，因为涉及到对象的强制转换，容易带来安全隐患。

**范例：**观察如下代码

```java
class A {
	public void print() {
		System.out.println("A、public void print()");
	}
}
class B extends A {
	public void print() {
		System.out.println("B、public void print()");
	}
}
public class MainClass {
	public static void main(String[] args) {
		A a = new A();
		B b = (B) a;
		b.print();
	}
}
```

```java
Exception in thread "main" java.lang.ClassCastException: A cannot be cast to B
	at MainClass.main(MainClass.java:14)

```

此时出现的异常表示的是类转换异常，指的是两个没有关系的类对象强制类转型是所带来的异常。所以进行强制类型转换是有风险的。

为了保证类型转换的顺利进行，在Java里面提供有一个关键字：instanceof，此关键字的使用方式：

```java
对象 instanceof 类  返回boolean型
```

如果某个对象是某个类的实例，那么就返回true否则返回false。

```java
class A {
	public void print() {
		System.out.println("A、public void print()");
	}
}
class B extends A {
	public void print() {
		System.out.println("B、public void print()");
	}
}
public class MainClass {
	public static void main(String[] args) {
		A a = new B();
		System.out.println(a instanceof A);
		System.out.println(a instanceof B);
		if (a instanceof B) {
			B b = (B) a; 
			b.print();
		}
	}
}
```

#### 总结

1、开发之中尽量使用向上转型，以统一参数类型，同时只有发生了向上转型之后才可以发生向下转型；

2、子类尽量不要扩充与父类无关的操作方法。

