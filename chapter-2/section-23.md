## 控制权限

* [单例设计模式](/chapter-2/section-23.md#单例设计模式)
* [多例设计模式](/chapter-2/section-23.md#多例设计模式)

---

### 访问控制权限

访问控制权限也就是针对于封装做了一个总结，本次来观察四种访问控制权限。

在之前只是简单的见到了封装性，但是对于封装性是与访问控制权限有直接联系的。

在Java里面一共支持四种访问控制权限。public、protected、default、private，而这四种访问控制权限特点：

| No. | 范围 | public | protected | default | private |
| :---: | :---: | :---: | :---: | :---: | :---: |
| 1 | 在同一个类中 | + | + | + | + |
| 2 | 在同一包的不同类 | + | + | + |  |
| 3 | 在不同包的子类 | + | + |  |  |
| 4 | 在不同包的非子类 | + |  |  |  |

除了public之外，对于封装可以使用private、protected、default，不过一般不会去考虑default。

对于其他的权限基本上已经见过了，现在重点放在protected，这种权限它直接与包的定义有关。

**范例：** 定义com.alpha.demoa.A类

```java
package com.alpha.demoa;
public class A {
	protected String info = "Hello";
}
```

**范例：** 定义com.alpha.demob.B类，此类继承A类

```java
package com.alpha.demob;
import com.alpha.demoa.A;
public class B extends A { // 是A不同包的子类
	public void print() {
		System.out.println("A类的info = " + super.info);
	}
}
```

**范例：** 测试

```java
package test;
import com.alpha.demob.B;
public class Test {
	public static void main(String[] args) {
		new B().print();	
	}
}
```

B中的print方法会输出A类中的info属性。

如果此时Test想要直接输出A类的info属性.

**范例：** 错误的访问

```java
package test;
import com.alpha.demoa.A;
public class Test {
	public static void main(String[] args) {
		A a = new A();
		System.out.println(a.info);
	}
}
```

由于Test与A类不在统一个包中，并且没有继承关系，所以无法访问protected属性。

#### 总结

1、Java的封装性是以private、protected、default三种权限的定义；

2、对于权限的声明，给出以下的建议：

* 声明属性就用private；
* 声明方法就用public；

**关于命名要求：**

* 类名称每一个单词的首字母大写，其余字母小写，例如StudentInfo；
* 属性名称第一个单词字母小写，而后每个单词首字母大写，例如：studentName；
* 方法名称第一个单词字母小写，而后每个单词首字母大写，例如：getName()；
* 常量名使用大写字母表示，例如：MESSAGE；
* 包名称使用小写字母，例如：com.alpha.util；

### 构造方法私有化

1、构造方法私有化的实现形式；

2、单例与多例设计模式；

### 单例设计模式

在正常情况下，一个类只有产生了对象之后才可以操作这个类。

**范例：** 观察如下代码

```java
class Singleton {
	public void print() {
		System.out.println("Hello World");
	}
}
public class MainClass {
	public static void main(String[] args) {
		Singleton s = null; // 声明对象
		s = new Singleton(); // 实例化对象
		s.print();
	}
}
```

现在Singleton类里面存在有构造方法，如果没有明确定义一个构造的话，会自动的在编译时生成一个无参构造方法，即：一个类至少会保留有一个构造方法。

**范例：** 修改Singleton类定义

```java
class Singleton {
	private Singleton() { // 构造方法私有化
	}
	public void print() {
		System.out.println("Hello World");
	}
}
```

一旦构造方法私有化，那么在外部就无法直接通过关键字new来进行对象的实例化操作。

**范例：** 错误的代码

```java
public class MainClass {
	public static void main(String[] args) {
		Singleton s = null; // 声明对象
		s = new Singleton(); // 实例化对象
		s.print();
	}
}
```

如何对代码进行改造使得外部类可以得到Singleton的实例对象。

**分析步骤：**

1、构造方法上使用了private声明则表示之歌构造方法可以在本类中进行调用，所以可以直接在Singleton中实例化一个对象；

```java
class Singleton {
	Singleton instance = new Singleton();
	private Singleton() {} // 构造方法私有化
	public void print() {
		System.out.println("Hello World");
	}
}
```

2、现在的singleton在Sinleton类里面只是一个普通的属性，而所有的类的普通属性必须在产生类的实例化对象之后才可以使用。此时可以使用static进行声明，让singleton属性直接由类进行调用。

```java
class Singleton {
	static Singleton instance = new Singleton();
	private Singleton() {} // 构造方法私有化
	public void print() {
		System.out.println("Hello World");
	}
}
public class MainClass {
	public static void main(String[] args) {
		Singleton s = null; // 声明对象
		s = Singleton.instance; // 直接访问static属性
		s.print();
	}
}
```

3、在一个类定义的时候应该对所有的属性进行封装

```java
private static Singleton instance = new Singleton();
```

4、访问封装之后的属性只能使用getter方法，那么就需要提供有一个getter方法返回Singleton实例对象。

```java
class Singleton {
	private static Singleton instance = new Singleton();
	private Singleton() {} // 构造方法私有化
	public static Singleton getInstance() {
		return instance;
	}
	public void print() {
		System.out.println("Hello World");
	}
}
public class MainClass {
	public static void main(String[] args) {
		Singleton s = null; // 声明对象
		s = Singleton.getInstance(); // 直接访问static属性
		s.print();
	}
}
```

**单例设计模式的意义：**

如果说想要控制一个类中实例化对象的个数，那么首先要控制的就是类中的构造方法。

既然需要一个实例化对象，那么就可以在类的内部使用static方式定义一个公共的对象，并且每次通过static方法获得的都是同一个对象，这样外部不管有多少次调用，那么最终一个类智能产生唯一的一个对象，这样的设计就属于单例设计模式（Singleton）。

**面试题：**请编写一个Singleton程序，这样做有什么特点。

```java
class Singleton {
	private static final Singleton INSTANCE = new Singleton();
	private Singleton() {}
	public static Singleton getInstance() {
		return INSTANCE;
	}
	public void print() {
		System.out.println("Hello World");
	}
}
public class MainClass {
	public static void main(String[] args) {
		Singleton s = Singleton.getInstance();
		s.print();
	}
}
```

程序特点：构造方法私有化，在类的内部定义static属性与方法，利用static方法取得本类的实例化对象，这样一来不管外部会产生多少个Singleton类对象，但是本质上永远只有唯一的一个实例。

对于单例设计模式有两种形式：饿汉式、懒汉式。

在之前就属于饿汉式的应用，在Singleton类定义的时候就已经准备好了一个Singleton类的实例化对象INSTANCE，而并没有关心这个对象是否使用。

而懒汉式的最大特点就在于它是在第一次使用的时候才进行实例化操作。

```java
class Singleton {
	private static Singleton instance;
	private Singleton() {}
	public static Singleton getInstance() {
		if (instance == null) {
			instance = new Singleton();
		}
		return instance;
	}
	public void print() {
		System.out.println("Hello World");
	}
}
```

单例只是一个理解过程，核心的目的只有一个：让一个类在整个系统里面只有一个实例化对象。

### 多例设计模式

既然存在有单例设计模式，那么就一定会有多例设计模式，单例设计只能让一个类产生一个实例化对象，而多例设计模式可以让一个类产生指定多个实例化对象。

例如：现在要顶一个表示一周时间数的，这个类只能够取七个对象；

例如：现在要定义有一个表示性别的类，这个类只能取两个对象。

**范例：** 定义一个表示性别的类

```java
class Sex {
	private String title;
	private static final Sex MALE = new Sex("男");
	private static final Sex FEMALE = new Sex("女");
	private Sex(String title) { // 构造方法私有化
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
	public static Sex getInstance(int ch) {
		switch (ch) {
			case 1: return MALE;
			case 2: return FEMALE;
			default: return null;
		}
	}
}
public class MainClass {
	public static void main(String[] args) {
		Sex sex = Sex.getInstance(2);
		System.out.println(sex);
	}
}
```

在JDK1.7之前，switch只能够利用int或char进行判断，但是正因为如果纯粹是数字或字符意义不明确，所以增加了String的支持。

```java
class Sex {
	private String title;
	private static final Sex MALE = new Sex("男");
	private static final Sex FEMALE = new Sex("女");
	private Sex(String title) { // 构造方法私有化
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
	public static Sex getInstance(String ch) {
		switch (ch) {
			case "man": return MALE;
			case "woman": return FEMALE;
			default: return null;
		}
	}
}
public class MainClass {
	public static void main(String[] args) {
		Sex sex = Sex.getInstance("man");
		System.out.println(sex);
	}
}
```

如果现在不希望使用String在switch语句上（实际上这个习惯并不好），那么可以引入一个标记接口。

```java
class Sex {
	private String title;
	private static final Sex MALE = new Sex("男");
	private static final Sex FEMALE = new Sex("女");
	private Sex(String title) { // 构造方法私有化
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
	public static Sex getInstance(int ch) {
		switch (ch) {
			case 1: return MALE;
			case 2: return FEMALE;
			default: return null;
		}
	}
}
interface Choose {
	public int MAN = 1;
	public int WOMAN = 2;
}
public class MainClass {
	public static void main(String[] args) {
		Sex sex = Sex.getInstance(Choose.MAN);
		System.out.println(sex);
	}
}
```

不提倡以上形式使用，在定义接口时其中的变量及方法都是具有特殊意义的。

#### 总结

1、单例设计模式就是一个类只能够产生唯一的一个实例化对象；

2.多例设计模式可以产生多个对象，要取得的时候需要加上标记。

不管是单例设计还是多例设计，核心：构造方法私有化。

