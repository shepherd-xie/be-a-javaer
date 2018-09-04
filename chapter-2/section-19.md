## 接口的定义及使用

---

所有的开发里面可以不使用抽象类，但是一定会使用接口。

1、接口的基本概念与主要特点；

2、接口的各个使用

3、工厂设计模式、代理设计模式。

### 接口的基本定义

如果一个类之中只是由抽象方法和全局常量所组成的，那么在这种情况下不会将其定义为一个抽象类，而只会将其定义为接口，所以所谓的接口严格来讲就属于一个特殊的类，而且这个类里面只有抽象方法与全局常量。

要定义一个接口使用interface关键字。

**范例：** 定义接口

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

**范例：** 实现接口

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
	interface A { <br />
	&nbsp;&nbsp;&nbsp;&nbsp;public static final String MSG = "Hello"; <br />
	&nbsp;&nbsp;&nbsp;&nbsp;public abstract void print(); <br />
	}
</td>
<td>
	interface A { <br />
	&nbsp;&nbsp;&nbsp;&nbsp;String MSG = "Hello"; <br />
	&nbsp;&nbsp;&nbsp;&nbsp;void print(); <br />
	}
</td>
</tr></table>

在接口里面没有写上public，其最终的访问权限也是public。为了方便阅读，强烈建议加上public。

对于接口的定义，大部分情况是以定义抽象方法为主，很少有接口只是单纯的去定义全局常量。

一个抽象类可以去继承一个抽象类，但是一个接口却可以使用extends关键字同时继承多个接口。（不能够继承抽象类）

**范例：** 观察接口的多继承

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

**范例：** 在接口里定义抽象类_**（代码仅用作示范，不会在开发中使用）**_

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

**范例：** 在一个接口内部如果使用了static去定义一个内部接口表示是一个外部接口。

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

**范例：** 定义USB标准

```java
// 标准可以连接不同层的操作类
interface USB { // 定义标准一定使用接口
	public void start();
	public void stop();
}
```

**范例：** 定义电脑

```java
class Computer {
	public void plugin(USB usb) { // 插入
		usb.start(); // 固定操作
		usb.stop();
	}
}
```

不管以后会有多少个设备，只要它是USB标准的实现子类，就都可以在电脑上使用。

**范例：** 定义U盘

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

**范例：** 定义打印机

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

### 接口的应用 —— 工厂设计模式（Factory）

下面观察一段程序代码。

```java
interface Fruit {
	public void eat();
}
class Apple implements Fruit {
	public void eat() {
		System.out.println("吃苹果");
	}
}
public class MainClass {
	public static void main(String[] args) {
		Fruit fruit = new Apple();
		fruit.eat();
	}
}
```

以上的程序可以通过主方法得到Fruit接口对象，但是有没有逻辑上的问题呢？

如果要确定一个代码是否合理，有如下几个标准：

* 客户端调用简单，不需要关注具体的细节；
* 客户端之外的代码修改，不影响客户的使用，即：用户可以不用关心代码是否变更。

以上程序没有语法错误，但关键问题出现在了关键字“new”上。一个接口不可能只有一个子类，所以对于Fruit也有可能产生多个子类对象。

```java
class Orange implements Fruit {
	public void eat() {
		System.out.println("吃橘子");
	}
}
```

现在的客户端上要想得到新的子类对象，需要修改代码。修改为：

```java
public class MainClass {
	public static void main(String[] args) {
		Fruit fruit = new Orange();
		fruit.eat();
	}
}
```

此时发现，如果在客户端产生了实例化对象，那么每次要想更换对象都需要修改客户端上的逻辑控制代码。这样的做法明显不是合理的。

在整个代码过程中，客户端应该关心的是如何取得一个Fruit接口对象，而后进行方法的调用，至于说这个借口对象的具体实例化子类是谁，并不是客户端所关心的。

所以经过分析发现，最大的问题就在于关键字new，而这一问题就可以理解为代码的耦合度过高。耦合度太高的直接问题就是代码不方便维护。如何解决这个问题可以参考Java虚拟机的设计思想：

* 程序 -> JVM -> 适应不同的操作系统（A -> C -> B）；

**范例：** 增加一个过渡

```java
class Factory {
	public static Fruit getInstance(String className) {
		if ("apple".equals(className)) {
			return new Apple();
		} else if ("oragle".equals(className)) {
			return new Orange();
		} else {
			return null;
		}
	}
}
public class MainClass {
	public static void main(String[] args) {
		Fruit fruit = Factory.getInstance("apple");
		fruit.eat();
	}
}
```

现在的客户端不会看见具体的子类，因为所有的接口对象都是通过Factory类取得的，如果日后要扩充新的Fruit子类对象，则只需要修改Factory类即可，但是客户端的调用不会发生变化。

![](/images/chapter-2/section-19/2.png)

**面试题：**编写一个Factory程序

### 接口的应用 —— 代理设计模式（Proxy）

![](/images/chapter-2/section-19/3.png)

```java
interface Subject { // 整个操作的核心主题
	public void doSomething();
}
class RealSubject implements Subject {
	public void doSomething() {
		System.out.println("顾客正在吃饭。");
	}
}
class ProxySubject implements Subject {
	private Subject subject;
	// 要接受一个真实主题的操作对象
	public ProxySubject(Subject subject) {
		this.subject = subject;
	}
	public void prepare() {
		System.out.println("为吃饭做准备");
	}
	public void doSomething() {
		this.prepare();
		this.subject.doSomething();
		this.destory();
	}
	public void destory() {
		System.out.println("打扫卫生");
	}
}
public class MainClass {
	public static void main(String[] args) {
		Subject subject = new ProxySubject(new RealSubject());
		subject.doSomething(); // 调用的是代理主题操作
	}
}
```

代理设计模式的核心就在于有一个主题操作接口（可能有多种方法），核心的业务主题只完成核心功能，例如：吃饭。而代理主题负责完成所有与核心主题有关的辅助性操作。

![](/images/chapter-2/section-19/4.png)

**面试题：**请编写一个Proxy程序

### 抽象类与接口的区别（面试题）

抽象类和接口在使用的形式上是非常相似的，所以很多人也乐意去问这两者之间的区别。

<table>
<thead>
<tr><td> No. </td><td> 区别 </td><td> 抽象类 </td><td> 接口 </td></td>
</thead>
<tbody>
<tr><td> 1 </td><td> 关键字 </td><td> abstract class </td><td> interface </td></tr>
<tr><td> 2 </td><td> 组成 </td><td> 构造方法、普通方法、抽象方法、static方法、常量、变量 </td><td> 抽象方法、全局常量 </td></tr>
<tr><td> 3 </td><td> 子类使用 </td><td> class 子类 extends 抽象类 </td><td> class 子类 implements 接口, 接口,... </td></tr>
<tr><td> 4 </td><td> 关系 </td><td> 抽象类可以实现多个接口 </td><td> 接口不能够继承抽象类，可以继承多个接口 </td></tr>
<tr><td> 5 </td><td> 权限 </td><td> 可以使用各种权限 </td><td> 只能够使用public权限 </td></tr>
<tr><td> 6 </td><td> 限制 </td><td> 单继承局限 </td><td> 没有单继承局限 </td></tr>
<tr><td> 7 </td><td> 子类 </td><td colspan="2"> 抽象类和接口都必须有子类，子类必须覆写全部的抽象方法 </td></tr>
<tr><td> 8 </td><td> 实例化对象 </td><td colspan="2"> 依靠子类对象的向上转型进行对象的实例化 </td></tr>
</tbody>
</table>

经过比较可以发现，抽象类中支持的功能绝对要比接口更多，但是有一点严重的缺陷：单继承局限。所以，在之后的开发之中，当抽象类和接口都可以使用的时候，优先考虑接口。

**一些不成文的规定（50%）：**

* 在进行某些公共操作的时候一定要定义出接口；
* 有了接口就需要利用子类完善方法；
* 如果是自己编写的接口，那么一定不要用new直接实例化接口子类，使用工厂类完成。

#### 总结

1、接口与抽象类定义的不同；

2、接口作为标准用于解耦合以及不同层之间的连接桥梁；

3、熟记工厂设计模式和代理设计模式。

