## 拓展概念（匿名内部类与包装类）

---

### 匿名内部类

接口与抽象类和匿名内部类的关系。

为什么需要匿名内部类，下面通过一个简短的代码来观察此概念。

```java
interface Message {
	public void print();
}
class MessageImpl implements Message {
	public void print() {
		System.out.println("Helo World !");
	}
} 
public class MainClass {
	public static void main(String[] args) {
		fun(new MessageImpl());
	}
	public static void fun(Message msg) {
		msg.print();
	}
}
```

正常的情况下，一个接口或者是抽象类需要有子类，子类要覆写所有的抽象方法。如果说此时MessageImpl子类只使用一次，那么还有必要为其单独定义一个实现类吗？所以这个时候就可以采用匿名内部类的方式进行代码的简化。

```java
interface Message {
	public void print();
}
public class MainClass {
	public static void main(String[] args) {
		fun(new Message() {
			public void print() {
				System.out.println("Hello World!");
			}
		});
	}
	public static void fun(Message msg) {
		msg.print();
	}
}
```

使用匿名内部类的时候有一个前提：必须要基于接口或抽象类的应用。

需要注意的是，如果匿名内部类定义在了方法里面，方法的参数或者是变量要被匿名内部类所访问，那么必须加上final关键字（JDK1.8之后此要求被改变）。

#### 总结

匿名内部类是在抽象类和接口的基础上发展的，匿名内部类的最大好处是帮助用户减少了类的定义。

### 包装类

1、基本数据类型包装类的定义；

2、数据的装箱与拆箱操作；

3、数据类型的转换。

### 认识包装类

Java在设计之初有一个基本原则：一切皆对象，一切的操作都要求用对象的形式进行描述。但是这里面就会出现一个矛盾，基本数据类型不是对象。那么为了符合这种要求，最早的时候可以采用人为的方式来解决此问题。

```java
class MyInt { // 一个类
	private int num; // 这个类包装的基本数据类型
	// 构造的目的是为了将基本数据类型传递给对象
	public MyInt(int num) { // 将基本类型包装起来
		this.num = num;
	}
	public int intValue() { // 将包装的数据内容返回
		return this.num;
	}
} 
public class MainClass {
	public static void main(String[] args) {
		MyInt mi = new MyInt(10); // 将int包装为对象
		int temp = mi.intValue(); // 将对象中包装的数据取出
		// 只有基本数据类型才可以进行计算
		System.out.println(temp * 2);
	}
}
```

在Java里面为了方便用户的操作，所以专门提供了一组包装类，来包装所有的基本数据类型：_byte（Byte）、short（Short）、int（Integer）、long（Long）、float（Float）、double（Double）、char（Character）、boolean（Boolean）_。

但是以上给出的包装类又分为两种子类型：

* 对象型包装类（Object直接子类）：Character、Boolean。
* 数值型包装类（Number直接子类）：Byte、Short、Integer、Long、Float、Double。

_Number是一个抽象类，里面一共定义了六个操作方法：intValue()、doubleValue()、floatValue()、byteValue()、shortValue()、longValue()。_

### 装箱与拆箱操作

现在已经存在有基本数据类型与包装类，那么这两种变量间的转换就通过以下方式定义：

* 装箱操作：将基本数据类型变为包装类的形式；
  * 每个包装类的构造方法都可以接收各自数据类型的变量；
* 拆箱操作：从包装类之中取出被包装的数据；
  * 利用Number类中提供的一系列xxxValue()方法完成。

**范例：**使用int和Integer

```java
public class MainClass {
	public static void main(String[] args) {
		Integer obj = new Integer(10); // 将基本数据类型装箱
		int temp = obj.intValue(); // 将基本数据类型拆箱
		System.out.println(temp * 2);
	}
}
```

之前所编写的MyInt类，现在换成了Integer这个系统类。

**范例：**使用double和Double

```java
public class MainClass {
	public static void main(String[] args) {
		Double obj = new Double(10.2); // 将基本数据类型装箱
		double temp = obj.doubleValue(); // 将基本数据类型拆箱
		System.out.println(temp * 2);
	}
}
```

**范例：**使用boolean和Boolean（不是Number子类）

```java
public class MainClass {
	public static void main(String[] args) {
		Boolean obj = new Boolean(true); // 将基本数据类型装箱
		boolean temp = obj.booleanValue(); // 将基本数据类型拆箱
		System.out.println(temp);
	}
}
```

现在可以发现，所有的包装类都使用了同样形式的方法进行操作。

在JDK1.5之前能够使用的操作都是以上形式的代码，但是从JDK1.5之后，Java为了方便开发提供了自动装箱与自动拆箱机制，并且可以直接利用包装类的对象进行数学计算。

**范例：**观察自动装箱与自动拆箱

```java
public class MainClass {
	public static void main(String[] args) {
		Integer obj = 10; // 自动装箱
		int temp = obj; // 自动拆箱
		obj ++; // 包装类直接进行数学计算
		System.out.println(temp * obj);
	}
}
```

如果可以直接使用包装类进行计算，就省略了手工的拆箱部分。

**注意：**在Integer类对象上发现可以直接赋予内容，也可以使用构造方法？

```java
public class MainClass {
	public static void main(String[] args) {
		Integer obja = 10; // 直接赋值
		Integer objb = 10; // 直接赋值
		Integer objc = new Integer(10);
		System.out.println(obja == objb); // true
		System.out.println(obja == objc); // false
		System.out.println(objb == objc); // false
		System.out.println(obja.equals(objc)); // true
	}
}
```

在使用包装类的时候很少会利用构造方法完成，几乎都是直接赋值（这一点与String相同），但是在判断内容是否相等的时候请一定要使用equals()方法。

**Tip：**此时Object可以一统天下了

Object可以接受一切引用数据类型，但是由于存在有自动的装箱机制，那么Object也可以存放基本数据类型了。

* 流程：基本数据类型 -> 自动装箱（成为对象） -> 向上转型为Object

```java
public class MainClass {
	public static void main(String[] args) {
		Object obj = 10; // 先包装在转换
		int temp = (Integer) obj; // 向下变为Integer然后再自动拆箱
		System.out.println(temp * 2);
	}
}
```

**范例：**观察Double

```java
public class MainClass {
	public static void main(String[] args) {
		Double obj = 10.2;
		System.out.println(obj * 2);
	}
}
```

**范例：**观察Booelan

```java
public class MainClass {
	public static void main(String[] args) {
		Boolean flag = true; // 自动装箱
		if (flag) { // 自动拆箱判断
			System.out.println("Hello World!");
		}
	}
}
```

有了自动装箱和拆箱的支持之后，在数据类型的选择上就方便了许多。

### 数据类型转换

使用包装类最多的情况实际上是它的数据类型转化功能上，在包装类里面提供有将String型数据变为基本数据类型的方法，使用几个代表类的类作说明：

* Integer类：public static int parseInt(String s);
* Double类：public static int parseDouble(String s);
* Boolean类：public static int parseBoolean(String s);

特别需要注意的是Character类里面并不存在有字符串变为字符的方法，因为String类有一个charAt()的方法可以根据索引取出字符内容。

**范例：**将字符串变为int型数据

```java
public class MainClass {
	public static void main(String[] args) {
		String str = "123"; // 字符串
		int temp = Integer.parseInt(str);
		System.out.println(temp * 2);
	}
}
```

此时实现了字符串变为基本数据类型的操作。但是在这样的转换过程之中一定要注意：被转换为数字的字符串一定要由数字组成。

**范例：**错误的代码

```java
public class MainClass {
	public static void main(String[] args) {
		String str = "1a3"; // 字符串
		int temp = Integer.parseInt(str);
		System.out.println(temp * 2);
	}
}
```

```java
Exception in thread "main" java.lang.NumberFormatException: For input string: "1a3"
```

**范例：**观察double转换

```java
public class MainClass {
	public static void main(String[] args) {
		String str = "1.3"; // 字符串
		double temp = Double.parseDouble(str);
		System.out.println(temp * 2);
	}
}
```

**范例：**观察boolean转换

```java
public class MainClass {
	public static void main(String[] args) {
		String str = "true"; // 字符串
		boolean flag = Boolean.parseBoolean(str);
		if (flag) {
			System.out.println(flag);
		} else {
			System.out.println(flag);
		}
	}
}
```

在Boolean进行转换的过程里面，如果要进行转换的字符串不是true或者是false那么将统一按照false处理。

现在既然实现了字符串变为基本数据类型的操作。那么也一定可以实现基本数据类型变为字符串的操作，此类操作有两种做法：

* 操作一：任何基本数据类型与字符串使用了“+”操作之后都表示变为字符串；

```java
public class MainClass {
	public static void main(String[] args) {
		int num = 100;
		String str = num + "";
		System.out.println(str.replaceAll("0", "9"));
	}
}
```

这样的操作虽然可以完成，但是会存在垃圾。

* 操作二：public static String valueOf(数据类型 变量);

```java
public class MainClass {
	public static void main(String[] args) {
		int num = 100;
		String str = String.valueOf(num);
		System.out.println(str.replaceAll("0", "9"));
	}
}
```

这样的转换不会产生垃圾，所以在开发的时候往往使用以上方法。

#### 总结

1、JDK1.5之后提供自动装箱与拆箱；

2、字符串与数据类型的互相转换：

* 字符串变为基本数据类型，依靠包装类的parseXxx()方法；
* 基本数据类型变为字符串，依靠String valueOf(数据类型 变量)方法。

