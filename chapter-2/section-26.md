## 泛型

---

1、泛型技术的产生背景；

2、泛型操作的实现；

3、通配符的使用。

### 泛型的引出

现在要求定义一个表示坐标的操作类(Point)，在这个类里面要求保存有以下几种坐标：

* 保存数字：x = 10、y = 20；
* 保存小数：x = 10.2、y = 20.3；
* 保存字符串：x = 东经20度、y = 北纬15度。

现在这个Point类设计的关键就在于x与y这两个变量的类型设计上。必须有一种类型可以保存这三类数据，那么先想到的一定是Object类型：

* int：int自动装箱为Integer，Integer向上转型为Object；
* double：double自动装箱为Double，Double向上转型为Object；
* String：直接向上转型为Object。

**范例：** 初期设计如下

```java
class Point { // 定义坐标
	private Object x;
	private Object y;
	public void setX(Object x) {
		this.x = x;
	}
	public void setY(Object y) {
		this.y = y;
	}
	public Object getX() {
		return x;
	}
	public Object getY() {
		return y;
	}
}
```

下面重复的演示三个程序，分别使用各个不同的数据类型。

**范例：** 在Point类里面保存整形数据

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point p = new Point();
		p.setX(10);
		p.setY(20);
		// 第二步：取出数据
		int x = (Integer) p.getX();
		int y = (Integer) p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

**范例：** 使用小数

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point p = new Point();
		p.setX(10.2);
		p.setY(20.3);
		// 第二步：取出数据
		double x = (Double) p.getX();
		double y = (Double) p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

**范例：** 使用字符串

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point p = new Point();
		p.setX("东经100度");
		p.setY("北纬20度");
		// 第二步：取出数据
		String x = (String) p.getX();
		String y = (String) p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

此时的代码已经利用Object数据类型解决了一切的开发问题，但是解决的关键靠的是Object，于是失败的关键也就是Object。

**范例：** 错误的程序

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point p = new Point();
		p.setX("东经100度");
		p.setY(10);
		// 第二步：取出数据
		String x = (String) p.getX();
		String y = (String) p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

```java
Exception in thread "main" java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
	at com.alpha.MainClass.main(MainClass.java:26)
```

这样的代码是明显存在问题的，因为在设置的时候存放的是int(Integer)而取的时候使用的是String，两个没有任何关系的类对象之间要发生强制转换，就一定会产生ClassCastException。

向上转型的核心目的是在于统一操作的参数上，而向下转型的目的是操作子类定义的特殊功能，可是现在的问题我们发现向下转型是一件非常不安全的操作，这一操作应该在代码运行之前就已经能够自动的排查出来这是最好的选择。可是之前的技术做不到。

从JDK1.5之后开始增加了泛型技术，而泛型技术的核心意义在于：类在定义的时候，可以使用一个标记，此标记表示类中属性或方法参数的类型标记，在使用的时候才动态的设置类型。

```java
// 此时设置的T在Point类定义上只表示一个标记，在使用的时候需要为其设置具体的类型
class Point<T> { // 定义坐标, T -> Type
	private T x; // 此属性的类型不知道，由Point类使用时动态决定
	private T y;
	public void setX(T x) {
		this.x = x;
	}
	public void setY(T y) {
		this.y = y;
	}
	public T getX() {
		return x;
	}
	public T getY() {
		return y;
	}
}
```

在使用Point类的时候才去设置标记的内容，也就是设置了类中的属性的类型。

**范例：** 设置为String

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point<String> p = new Point<String>();
		p.setX("东经100度");
		p.setY("北纬20度");
		// 第二步：取出数据，由于接受的类型就是String所以不需要强制转型
		String x = p.getX();
		String y = p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

发现使用了泛型之后，所有类型属性的类型都是动态设置的，而所有使用泛型标记的方法参数也都发生改变，这样就相当于避免了向下转型的问题，从而解决了类转换的安全隐患。

但是需要特别说明的是，如果想要使用泛型，那么它能够采用的类型只能够是类，即：不能是基本类型，只能是引用类型。

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point<Integer> p = new Point<Integer>();
		p.setX(10);
		p.setY(20);
		// 第二步：取出数据
		int x = p.getX();
		int y = p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

对于泛型有两点说明：

* 如果在使用泛型类或者是接口的时候，没有设置泛型的具体类型，那么会出现编译时的警告，同时为了保证程序不出错，所有的泛型都将使用Object表示。

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point p = new Point(); // 表示默认使用Object进行类型的设置
		p.setX(10);
		p.setY(20);
		// 第二步：取出数据
		int x = (Integer) p.getX();
		int y = (Integer) p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

* 从JDK1.7开始可以简化声明泛型。

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据
		Point<Integer> p = new Point<>(); // JDK1.7之后实例化的泛型可以省略
		p.setX(10);
		p.setY(20);
		// 第二步：取出数据
		int x = p.getX();
		int y = p.getY();
		System.out.println("x坐标：" + x + "，y坐标：" + y);
	}
}
```

### 通配符

为了更好地理解通配符的作用，下面首先来观察一个程序。

```java
package com.alpha;
class Message<T> {
	private T msg;
	public void setMsg(T msg) {
		this.msg = msg;
	}
	public T getMsg() {
		return msg;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Message<String> m = new Message<String>();
		m.setMsg("Hello World!");
		fun(m);
	}
	public static void fun(Message<String> temp) {
		System.out.println(temp.getMsg());
	}
}
```

以上的代码为Message类设置的是一个String型的泛型类型，但是如果说现在设置的是其他类型的呢？

fun()方法里面接收的“Message<String>”那么就不能够使用了，并且fun()方法不能够针对于不同的泛型进行重载，因为方法的重载认得只是参数的类型，与参数无关。

**解决方法一：**不设置方法参数的泛型

```java
public class MainClass {
	public static void main(String[] args) {
		Message<String> m1 = new Message<String>();
		Message<Integer> m2 = new Message<Integer>();
		m1.setMsg("Hello World!");
		m2.setMsg(100);
		fun(m1);
		fun(m2);
	}
	public static void fun(Message temp) {
		System.out.println(temp.getMsg());
	}
}
```

此时在fun()方法上依然存在有警告信息，因为只要不设置泛型，就一定会存在警告信息。如果不设置泛型，就有可能出现以下错误：

```java
public class MainClass {
	public static void main(String[] args) {
		Message<String> m1 = new Message<String>();
		Message<Integer> m2 = new Message<Integer>();
		m1.setMsg("Hello World!");
		m2.setMsg(100);
		fun(m1);
		fun(m2);
	}
	public static void fun(Message temp) { // 不设置泛型默认为Object
		temp.setMsg("Hello"); // 设置为String型
		System.out.println(temp.getMsg());
	}
}
```

所以现在最应该解决的是，需要有一种方式可以接受一个类的任意的泛型类型，但是不可以修改只能够取出，那么就可以使用“?”来描述。

```java
public class MainClass {
	public static void main(String[] args) {
		Message<String> m1 = new Message<String>();
		Message<Integer> m2 = new Message<Integer>();
		m1.setMsg("Hello World!");
		m2.setMsg(100);
		fun(m1);
		fun(m2);
	}
	public static void fun(Message<?> temp) { // 不能够设置但是可以取出
		System.out.println(temp.getMsg());
	}
}
```

在“?”通配符基础上还会有两个子的通配符：

* ? extends 类：设置泛型上限，可以在声明上和方法参数上使用；
  * ? extends Number：意味着可以设置Number或者是Number的子类(Integer、Double、...)
* ? super 类：设置泛型下限，方法参数上使用；
  * ? super String：意味着只能够设置String或者是它的父类Object。

**范例：** 设置泛型的上限

```java
package com.alpha;
class Message<T extends Number> {
	private T msg;
	public void setMsg(T msg) {
		this.msg = msg;
	}
	public T getMsg() {
		return msg;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Message<Integer> m = new Message<Integer>();
		m.setMsg(100);
		fun(m);
	}
	public static void fun(Message<? extends Number> temp) { // 不能够设置但是可以取出
		System.out.println(temp.getMsg());
	}
}
```

如果设置了非Number或者是其子类的话，那么将出现语法错误。

**范例：** 设置泛型的下限

```java
package com.alpha;
class Message<T> {
	private T msg;
	public void setMsg(T msg) {
		this.msg = msg;
	}
	public T getMsg() {
		return msg;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Message<String> m = new Message<String>();
		m.setMsg("Hello");
		fun(m);
	}
	public static void fun(Message<? super String> temp) { // 不能够设置但是可以取出
		System.out.println(temp.getMsg());
	}
}
```

对于这些概念要求能够看懂就行了，至于说具体的代码实际编写的情况并不多。

### 泛型接口

在之前都是将泛型定义在了一个类里面，那么泛型也可以在接口上声明，称为泛型接口。

**范例：** 定义泛型接口

```java
// 如果是接口在前面加上字母I，例如：IMessage
// 如果是抽象类在前面加上Abstract，例如：AbstractMessage
// 如果是普通类直接编写，例如：Message
interface IMessage<T> { // 设置泛型接口
	public void print(T t);
}
```

在接口上必须要定义其相应的子类，所以来讲如果要定义子类有两种形式。

**形式一：**在子类继续设置泛型

```java
package com.alpha;
// 如果是接口在前面加上字母I，例如：IMessage
// 如果是抽象类在前面加上Abstract，例如：AbstractMessage
// 如果是普通类直接编写，例如：Message
interface IMessage<T> { // 设置泛型接口
	public void print(T t);
}
// 子类也继续使用泛型，并且父借口使用和子类同样的泛型标记
class MessageImpl<T> implements IMessage<T> {
	public void print(T t) {
		System.out.println(t);
	}
}
public class MainClass {
	public static void main(String[] args) {
		IMessage<String> msg = new MessageImpl<String>();
		msg.print("Hello World !");
	}
}
```

**形式二：**在子类不设置泛型，而为父接口明确的定义一个泛型类型

```java
package com.alpha;
// 如果是接口在前面加上字母I，例如：IMessage
// 如果是抽象类在前面加上Abstract，例如：AbstractMessage
// 如果是普通类直接编写，例如：Message
interface IMessage<T> { // 设置泛型接口
	public void print(T t);
}
// 子类也继续使用泛型，并且父借口使用和子类同样的泛型标记
class MessageImpl implements IMessage<String> {
	public void print(String t) {
		System.out.println(t);
	}
}
public class MainClass {
	public static void main(String[] args) {
		IMessage<String> msg = new MessageImpl();
		msg.print("Hello World !");
	}
}
```

### 泛型方法

泛型方法不一定非要定义在支持泛型的类李曼。在之前所编写的所有存在有泛型的方法都是在泛型支持类里面定义的。

**范例：** 泛型方法定义

```java
package com.alpha;
public class MainClass {
	public static void main(String[] args) {
		String str = fun("Hello");
		System.out.println(str);
	}
	// T的类型由传入的参数类型决定
	public static <T> T fun(T t) {
		return t;
	}
}
```

能够看懂泛型方法的标记就行了。

#### 总结

1、泛型解决的是向下转型所带来的安全隐患，其核心的组成就是在声明类或接口的时候不设施参数或属性的类型；

2、“?”可以接受任意的泛型类型，只能够取出，但是不能够修改。

