## 接口的定义及使用

---

所有的开发里面可以不使用抽象类，但是一定会使用接口。

1、接口的基本概念与主要特点；

2、接口的各个使用

3、工厂设计模式、代理设计模式。

### 接口的基本定义

如果一个类之中只是由抽象方法和全局常量所组成的，那么在这种情况下不会将其定义为一个抽象类，而只会将其定义为接口，所以所谓的接口严格来讲就属于一个特殊的类，而且这个类里面只有抽象方法与全局常量。

要定义一个接口使用interface关键字。

**范例：**定义接口

```java
interface A { // 定义接口
	public static final String MSG = "Hello"; // 全局常量
	public abstract void print(); // 抽象方法
}
```

由于接口里面存在有抽象方法，所以接口对象不可能直接使用new进行实例化的操作，所以接口的使用原则如下：

* 接口必须有子类，一个子类可以使用implements关键字实现多个接口；
* 接口的子类（如果不是抽象类），必须覆写接口中的全部抽象方法；
* 接口的对象可以利用对象的向上转型进行实例化操作。

**范例：**实现接口

```java
interface A { // 定义接口
	public static final String MSG = "Hello"; // 全局常量
	public abstract void print(); // 抽象方法
}
interface B {
	public abstract void get();
}
class X implements A, B { // X类实现了A和B两个接口
	public void get() {
		System.out.println("A接口的抽象方法");
	}
	public void print() {
		System.out.println("B接口的抽象方法");
	}
}
public class MainClass {
	public static void main(String[] args) {
		X x = new X(); // 实例化子类对象
		A a = x; // 向上转型
		B b = x; // 向上转型
		a.print();
		b.get();
	}
}
```

以上的代码实例化了X类对象。现在由于X是A和B的子类，那么X类的对象可以变为A接口或B接口的对象。

```java
public class MainClass {
	public static void main(String[] args) {
		A a = new X();
		System.out.println(a instanceof A);
		System.out.println(a instanceof B);
	}
}
```

在定义结构上来讲A和B接口没有任何的直接联系，但是这两个接口却同时拥有一个子类：X子类。因为最终实例化的是X子类，而这个子类是属于B接口的。

对于子类而言，除了接口之外还有可能去继承抽象类，所以说一个子类既要继承抽象类又要实现接口的话，先使用extends继承，然后再用implements实现接口。

```java
interface A { // 定义接口
	public static final String MSG = "Hello"; // 全局常量
	public abstract void print(); // 抽象方法
}
interface B {
	public abstract void get();
}
abstract class C {
	public abstract void change();
}
class X extends C implements A, B { // X类实现了A和B两个接口
	public void get() {
		System.out.println("A接口的抽象方法");
	}
	public void print() {
		System.out.println("B接口的抽象方法");
	}
	public void change() {
		System.out.println("C类的抽象方法");
	}
}
```

对接口而言，其中的组成部分只有抽象方法和全局常量，所以很多时候一些人为了省略编写，可以不用写上public abstract或public static final，并且在方法上是否编写public结果都是一样的，因为在接口里面只能够使用一种访问权限 —— public。以下两个接口的定义最终效果是完全相同的：

<table><tr>
<td>
	interface A {
	&nbsp;&nbsp;&nbsp;&nbsp;public static final String MSG = "Hello";
	&nbsp;&nbsp;&nbsp;&nbsp;public abstract void print();
	}
</td>
<td>
	interface A {
	&nbsp;&nbsp;&nbsp;&nbsp;String MSG = "Hello";
	&nbsp;&nbsp;&nbsp;&nbsp;void print();
	}
</td>
</tr></table>

在接口里面没有写上public，其最终的访问权限也是public。为了方便阅读，强烈建议加上public。

对于接口的定义，大部分情况是以定义抽象方法为主，很少有接口只是单纯的去定义全局常量。

一个抽象类可以去继承一个抽象类，但是一个接口却可以使用extends关键字同时继承多个接口。（不能够继承抽象类）

**范例：**观察接口的多继承

```java
interface A {
	public void funA();
}
interface B {
	public void funB();
}
// C接口同时继承了A和B两个接口
interface C extends A, B { // 此处使用extends
	public void funC();
}
class X implements C {
	public void funA() {}
	public void funB() {}
	public void funC() {}
}
```

从继承关系上讲抽象类的限制比接口多太多：

* 一个抽象类只能够继承一个父类，而接口没有这个限制；
* 一个子类只能继承一个父类，却可以实现多个接口。

在整个Java里面，接口的主要功能是解决单继承局限的问题。

虽然从接口本身的概念上来讲只能够由抽象方法和全局常量组成但是所有的内部结构不受到这些要求的，也就是说接口里面可以定义普通内部类、抽象内部类、内部接口。

**范例：**在接口里定义抽象类_**（代码仅用作示范，不会在开发中使用）**_

```
interface A {
	public void funA();
	abstract class B {
		public abstract void funB();
	}
}
class X implements A { // 实现了A接口
	public void funA() {
		System.out.println("Hello World");
	}
	class Y extends B { // 内部抽象类的子类
		public void funB() {}
	}
}
```

**范例：**在一个接口内部如果使用了static去定义一个内部接口表示是一个外部接口。

```java
interface A {
	public void funA();
	static interface B { // 外部接口
		public void funB();
	}
}
class X implements A.B {
	public void funB() {}
}
```

**先期总结：**接口在实际的开发之中有三大核心作用

* 定义不同层之间的操作标准；
* 表示一种操作的能力；
* 表示将服务段的远程方法视图暴露给客户端。

### 接口的实际应用 —— 标准

电脑上可以使用U盘、MP3、打印机，这些设备都是连接到USB接口上的。

![](/images/chapter-2/section-19/1.png)

所有的代码如果要进行开发，一定要首先开发出USB接口标准，因为有了标准之后，电脑才可以去使用这些标准，设备厂商才可以去设计USB设备。

**范例：**定义USB标准

```java
// 标准可以连接不同层的操作类
interface USB { // 定义标准一定使用接口
	public void start();
	public void stop();
}
```

**范例：**定义电脑

```java
class Computer {
	public void plugin(USB usb) { // 插入
		usb.start(); // 固定操作
		usb.stop();
	}
}
```

不管以后会有多少个设备，只要它是USB标准的实现子类，就都可以在电脑上使用。

**范例：**定义U盘

```java
class Flash implements USB {
	public void start() {
		System.out.println("U盘开始使用");
	}
	public void stop() {
		System.out.println("U盘停止使用");
	}
}
```

**范例：**定义打印机

```java
class Print implements USB {
	public void start() {
		System.out.println("打印机开始使用");
	}
	public void stop() {
		System.out.println("打印机停止使用");
	}
}
```

按照这样的形式可以准备出无数的子类，并且这无数个子类都可以在电脑的plugin\(\)方法上使用。

```java
public class MainClass {
	public static void main(String[] args) {
		Computer com = new Computer();
		com.plugin(new Flash());
		com.plugin(new Print());
	}
}
```

此时可以很好的描述出显示的关系。

在现实生活中，标准的概念随处可见，而在程序之中标准就是利用接口来定义的。

```java
```

