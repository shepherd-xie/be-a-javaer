## 枚举

---

~~**_知道就行了，用不用随意，反正我不用。_**~~在某些场景中枚举的使用可以起到事半功倍的效果。

1、枚举的基本用法

2、枚举的定义组成

### 认识枚举

**多例设计模式：**

构造方法私有化，而后在类的内部提供有若干个实例化对象，并且通过static方法返回。

**范例：**定义一个表示颜色基色的多例

```java
package com.alpha;
class Color {
	private String title;
	private static final Color RED = new Color("红色");
	private static final Color GREEN = new Color("绿色");
	private static final Color BLUE = new Color("蓝色");
	private Color(String title) {
		this.title = title;
	}
	public static Color getInstance(int ch) {
		switch (ch) {
		case 1:
			return RED;
		case 2:
			return GREEN;
		case 3:
			return BLUE;
		default:
			return null;
		}
	}
	public String toString() {
		return this.title;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Color red = Color.getInstance(1);
		System.out.println(red);
	}
}
```

严格来讲，各个语言都支持枚举（除了2005年之前的Java），也就是说在2005年之前，要是想定义枚举都会采用以上的形式代码完成。

从2005年之后，增加了枚举的概念，同时设置了一个新的enum关键字。

**范例：**定义枚举

```java
package com.alpha;
enum Color { // 定义枚举类
	RED, GREEN, BLUE;
}
public class MainClass {
	public static void main(String[] args) {
		Color red = Color.RED;
		System.out.println(red);
	}
}
```
```java
package com.alpha;
enum Color { // 定义枚举类
	红色, 绿色, 蓝色;
}
public class MainClass {
	public static void main(String[] args) {
		Color red = Color.红色;
		System.out.println(red);
	}
}
```

可以发现使用枚举之后可以完全简化的替代多例设计模式。

但是需要说明的是，枚举严格来讲并不是一个新的功能，在Java里面虽然使用了enum关键字定义了枚举，但是使用enum定义的枚举就相当于是一个类继承了Enum类而已。

```java
public abstract class Enum<E extends Enum<E>>
extends Object
implements Comparable<E>, Serializable
```

Enum是一个抽象类，里面定义的构造如下：

```java
protected Enum(String name, int ordinal)
```

Enum类的构造方法依然是被封装的，所以也属于后遭方法私有化的应用范畴，所有的多例实际模式前提：构造方法私有化。

在Enum类里面定义了两个方法：

* 取得枚举的索引：public final int ordinal()；
* 取得枚举的名字；public final String name()。

除了以上支持的方法之外，使用enum关键字定义的枚举类里面还有一个values()方法，这个方法将枚举对象以对象数组的形式全部返回。

```java
package com.alpha;
enum Color {
	RED, GREEN, BLUE;
}
public class MainClass {
	public static void main(String[] args) {
		for (Color c : Color.values()) {
			System.out.println(c.ordinal() + " - " + c.name());
		}
	}
}
```

**面试题：**请解释enum和Enum的区别？

* enum是一个关键字，而Enum是一个抽象类；
* 使用enum定义的枚举就相当于一个类继承了Enum这个抽象类。

### 定义其他结构

虽然发现枚举的定义比较简单，可是对于之前讲解的多例设计模式里面还可以在类中定义属性和方法等，实际上枚举也可以，可是有一点要求：

* 枚举之中定义的构造方法不能够使用public声明，如果没有无参构造，请手工调用构造传递参数；
* 枚举对象必须要放在首行，随后才可以定义属性、构造、普通方法。

**范例：**扩充枚举功能

```java
package com.alpha;
enum Color {
	RED("红色"), GREEN("绿色"), BLUE("蓝色");
	private String title;
	private Color(String title) {
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
}
public class MainClass {
	public static void main(String[] args) {
		for (Color c : Color.values()) {
			System.out.println(c);
		}
	}
}
```

此时与之前定义的多例设计模式操作方式完全相同，而且代码更加简短。

枚举还可以实现接口。

**范例：**枚举实现接口

```java
package com.alpha;
interface Message {
	public String getTitle();
}
enum Color implements Message {
	RED("红色"), GREEN("绿色"), BLUE("蓝色");
	private String title;
	private Color(String title) {
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
	public String getTitle() {
		return this.title;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Message msg = Color.RED;
		System.out.println(msg.getTitle());
	}
}
```

枚举里面最变态的一种做法是可以在每一个对象后面使用匿名内部类的形式实现抽象方法。

**范例：**另外一种接口的实现

```java
package com.alpha;
interface Message {
	public String getTitle();
}
enum Color implements Message {
	RED("红色") {
		public String getTitle() {
			return "自己的" + this;
		}
	}, GREEN("绿色") {
		public String getTitle() {
			return "自己的" + this;
		}
	}, BLUE("蓝色") {
		public String getTitle() {
			return "自己的" + this;
		}
	};
	private String title;
	private Color(String title) {
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
	public String getTitle() {
		return this.title;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Message msg = Color.RED;
		System.out.println(msg.getTitle());
	}
}
```

更变态的是枚举里面还可以直接定义抽象方法。此时每一个枚举对象必须分别覆写抽象方法。

**范例：**定义抽象方法并覆写

```java
package com.alpha;
enum Color {
	RED("红色") {
		public String getTitle() {
			return "自己的" + this;
		}
	}, GREEN("绿色") {
		public String getTitle() {
			return "自己的" + this;
		}
	}, BLUE("蓝色") {
		public String getTitle() {
			return "自己的" + this;
		}
	};
	private String title;
	private Color(String title) {
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
	public abstract String getTitle();
}
public class MainClass {
	public static void main(String[] args) {
		System.out.println(Color.RED.getTitle());
	}
}
```

整个过程里面发现枚举的功能真的很强大，强大到没人使了。

### 枚举的实际作用

枚举可以直接在switch上使用。

```java
package com.alpha;
enum Color {
	RED, GREEN, BLUE;
}
public class MainClass {
	public static void main(String[] args) {
		Color c = Color.RED;
		switch(c) {
		case RED:
			System.out.println("这是红色");
			break;
		case GREEN:
			System.out.println("这是绿色");
			break;
		case BLUE:
			System.out.println("这是蓝色");
			break;
		}
	}
}
```

尝试正常的使用枚举。

```java
package com.alpha;
enum Sex {
	MALE("男"), FEMALE("女");
	private String title;
	private Sex(String title) {
		this.title = title;
	}
	public String toString() {
		return this.title;
	}
}
class Person {
	private String name;
	private int age;
	private Sex sex;
	public Person(String name, int age, Sex sex) {
		this.name = name;
		this.age = age;
		this.sex = sex;
	}
	public String toString() {
		return "Person [name=" + name + ", age=" + age + ", sex=" + sex + "]";
	}
	
}
public class MainClass {
	public static void main(String[] args) {
		System.out.println(new Person("Omega", 24, Sex.MALE));
	}
}
```

不使用枚举也同样可以实现功能。

#### 总结

1、枚举属于高级的多例设计模式；

2、如果你没习惯使用枚举，那么就别习惯了，如果你已经习惯了，那么就继续使用。

