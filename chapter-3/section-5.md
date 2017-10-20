## Java基础类库

---

### StringBuffer类

1、StringBuffer类的主要特点；

2、StringBuffer、StringBuilder、String的区别。

【回顾】String类的特点：

* String类对象有两种实例化方式：
  * 直接赋值：只开辟一块堆内存空间，可以自动入池；
  * 构造方法：开辟两块堆内存空间，不会自动入池，使用intern()手工入池。
* 任何一个字符串都是String类的匿名对象；
* 字符串一旦声明则不可改变，可以改变的只是String类对象的引用。

虽然在所有的项目里面，String类都一定要用，可是String类有一个问题不得不进行重复，那么就是String类的内容不可改变。为此在Java里面提供有另外一个类 —— StringBuffer类（里面的内容可以修改）。

String类的对象可以使用“+”进行字符串的连接操作，但是在StringBuffer类里面必须使用append()方法进行追加：

* 方法：public StringBuffer append(Object obj)

**范例：**观察StringBuffer基本使用

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		// String类可以直接赋值实例化，但是StringBuffer类不行
		StringBuffer sb = new StringBuffer();
		sb.append("Hello ").append("World ").append("!");
		change(sb); // 引用传递
		System.out.println(sb);
	}
	public static void change(StringBuffer temp) {
		temp.append("\n").append("Hello mori.");
	}
}
```

发现StringBuffer类的内容是可以进行修改的，而String的内容是不可以修改的。

清楚了StringBuffer类的基本使用与基本操作之后，下面来继续观察一下这两个类的定义情况。

<table>
<tr>
<td><b>String类：</b></td>
<td><b>StringBuffer类：</b></td>
</tr>
<tr>
<td>
public final class String </ br>
extends Object </ br>
implements Serializable, Comparable<String>, CharSequence </ br>
</td>
<td>
public final class StringBuffer </ br>
extends Object </ br>
implements Serializable, CharSequence </ br>
</td>
</tr>
</table>

发现String与StringBuffer都是CharSequence接口的子类。而在以后的开发之中，如果看见某些方法的操作上出现的是CharSequence接口，那么只要传递字符串即可。

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		CharSequence seq = "Hello"; // 向上转型
		System.out.println(seq); // String类覆写的toString()
	}
}
```

虽然String和StringBuffer类有着共同的接口，但是这两个类对象之间如果要转换不能够直接转换。

1、将String变为StringBuffer

* 方式一：利用StringBuffer类的构造方法
  * public StringBuffer(String str)

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		StringBuffer sb = new StringBuffer("Hello"); // String变为StringBuffer
		System.out.println(sb);
	}
}
```

* 方式二：利用append()方法
  * public StringBuffer append(String str)

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		StringBuffer sb = new StringBuffer(); 
		sb.append("Hello"); // String变为StringBuffer
		System.out.println(sb);
	}
}
```

2、将StringBuffer类变为String

* 利用toString()方法可以将StringBuffer转换为String

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		StringBuffer sb = new StringBuffer("Hello"); 
		String str = sb.toString();
		System.out.println(str);
	}
}
```

也可以利用String类的构造方法实现StringBuffer与String的转换：public String(StringBuffer buffer)

在String类里面也提供有一个和StringBuffer比较的方法：public boolean contentEquals(StringBuffer sb)

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		StringBuffer sb = new StringBuffer("Hello"); 
		System.out.println("Hello".contentEquals(sb));
	}
}
```

String类之中定义了许多方便于用户的开发，而在StringBuffer类里面也定义了许多的操作方法，而且部分方法与String类是正好互补的。

1、字符串反转：public StringBuffer reverse()

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		StringBuffer sb = new StringBuffer("Hello"); 
		System.out.println(sb.reverse());
	}
}
```

2、在指定的索引位置增加数据：public StringBuffer insert(int offset, Object obj)

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		StringBuffer sb = new StringBuffer("Hello"); 
		sb.insert(0, "mori ").insert(0, "Mr. ");
		System.out.println(sb);
	}
}
```

3、删除部分数据：public StringBuffer delete(int start, int end)

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		StringBuffer sb = new StringBuffer("Hello World mori"); 
		System.out.println(sb.delete(5, 11));
	}
}
```

 从JDK1.5之后增加了一个新的字符串操作类：StringBuilder，这个类的定义结构如下：

```java
public final class StringBuilder
extends Object
implements Serializable, CharSequence
```

发现StringBuffer类与StringBuilder类在定义上非常的相似，几乎连方法也一样。

**面试题：**请解释String、StringBuffer、StringBuilder的区别？

* String的内容一旦声明则不可改变，而StringBuffer与StringBuilder声明的内容可以改变；
* StringBuffer类中提供的方法都是同步方法，属于安全的线程操作，而StringBuilder类中的方法都属于异步方法，属于非线程安全的操作。

日后在开发之中，如果见到了字符串的应用，不需要思考，95%使用的都是String类，只有在需要频繁修改的时候才会考虑到StringBuffer或者是StringBuilder类操作。

#### 总结

String类依然是最常用的字符串描述类，而StringBuffer类由于出现时间较长，使用要比StringBuilder多。

### Runtime类

1、Runtime类的主要作用；

2、Runtime类的定义形式。

在每一个JVM进程里面都会存在有一个Runtime类的对象，这个类的主要功能是取得一些与运行时有关的环境属性或者创建新的进程等操作。

在Runtime类定义的时候它的构造方法已经被私有化了，这就属于单例设计模式的应用，因为要保证在整个进程里面只有唯一的一个Runtime类对象。所以在Runtime类里面提供有一个static型的方法，这个方法可以取得Runtime类的实例化对象：public static Runtime getRuntime()。

Runtime类是直接与本地运行有关的所有相关属性的集合，所以在Runtime类里面定义有如下的方法：

* 返回所有可用内存空间：public long totalMemory()；
* 返回最大可用内存空间：public long maxMemory()；
* 返回空余内存空间：public long freeMemory()。

**范例：**观察内存大小

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		Runtime run = Runtime.getRuntime(); // 取得Runtime类的实例化对象
		System.out.println("1、MAX = " + run.maxMemory());
		System.out.println("1、TOTAL = " + run.totalMemory());
		System.out.println("1、FREE = " + run.freeMemory());
		String str = "";
		for (int i = 0; i < 2000; i ++) {
			str += i;
		}
		System.out.println("2、MAX = " + run.maxMemory());
		System.out.println("2、TOTAL = " + run.totalMemory());
		System.out.println("2、FREE = " + run.freeMemory());
	}
}
```

如果一旦产生了过多的垃圾之后，那么就会改变可用内存空间的大小。

在Runtime类里面有一个方法：public void gc()，可以释放掉垃圾空间。

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) {
		Runtime run = Runtime.getRuntime(); // 取得Runtime类的实例化对象
		System.out.println("1、MAX = " + run.maxMemory());
		System.out.println("1、TOTAL = " + run.totalMemory());
		System.out.println("1、FREE = " + run.freeMemory());
		String str = "";
		for (int i = 0; i < 2000; i ++) {
			str += i;
		}
		System.out.println("2、MAX = " + run.maxMemory());
		System.out.println("2、TOTAL = " + run.totalMemory());
		System.out.println("2、FREE = " + run.freeMemory());
		run.gc();
		System.out.println("3、MAX = " + run.maxMemory());
		System.out.println("3、TOTAL = " + run.totalMemory());
		System.out.println("3、FREE = " + run.freeMemory());
	}
}
```

**面试题：**请解释什么叫GC？如何处理？

* GC（Garbage Collector）垃圾收集器，指的是释放无用的内存空间；
* GC会由系统不定期进行自动的回收，或者调用Runtime类中的gc()方法收工回收。

实际上Runtime类还有一个更加有意思的功能，就是说他可以调用本机的可执行程序，并且创建进程。

* 执行程序：public Process exec(String command) throws IOException.

**范例：**执行进程

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Runtime run = Runtime.getRuntime(); // 取得Runtime类的实例化对象
		Process pro = run.exec("calc"); // 调用本机可执行程序
		Thread.sleep(2000);
		pro.destroy(); // 销毁进程
	}
}
```

#### 总结

1、Runtime类使用了单例设计模式，必须通过内部的getRuntime()方法才可以取得Runtime类对象；

2、Runtime类提供了gc()方法，可以用于手工释放内存。

### System类

1、如何计算某个代码的执行时间；

2、进行垃圾收集操作

之前一直使用的“System.out.println()”就属于System类的操作功能，只不过这个功能由于牵扯到IO部分，所以留到以后继续讲解。在System类里面之前也使用过一个System.arraycopy()方法实现数组拷贝，而这个方法的真实定义如下：

* 数组拷贝：public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)。

在System类里面定义有一个重要的方法：

* 取得当前的系统时间：public static long currentTimeMillis()。

**范例：**请统计出某项操作的执行时间

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		long start = System.currentTimeMillis(); // 取得开始时间
		String str = "";
		for (int i = 0; i < 30000; i ++) {
			str += i;
		}
		long end = System.currentTimeMillis(); // 取得结束时间
		System.out.println("本次操作所花费时间：" + (end - start));
	}
}
```

如果要想统计出所花费的毫秒时间，就用long型数据直接进行数学计算。

在System类里面定义了一个操作方法：public static void gc()，这个gc()方法并不是一个新定义的方法，而是间接调用了Runtime类中的gc()方法。

对象产生一定会调用构造方法，可以进行一些处理操作，相对应的也存在有对象销毁时所调用的方法（类似C++中的析构函数），如果某个对象需要此类的功能，那么就可以考虑覆写Object类中的finalize()方法。

> ### 析构函数
>
> 一个析构函数是一个构造函数的逆，当一个类的一个实例被销毁时会被调用，例如当一个类在块（一组花括号“{}”)中被构造的一个对象会在关闭括号后删除，之后析构函数被自动调用。它会在清空保存变量的内存位置时被调用。析构函数可以在类被销毁时用来释放资源，例如堆分配的内存和打开的文件。
>
> 声明一个析构函数的符号类似于构造函数。它没有返回值而且方法的名称和在类的名称前加上波浪线（~）相同。
>
> ```c++
> ~person() 
> {
>     cout << "I'm deleting " << name << " with age " << age << endl;
> }
> ```
>
> 另外要注意的是，析构函数是不容许参数传递。然而，与构造函数一样，析构函数可以被显式调用：
>
> ```c++
> int main() 
> {
>     person someone("Wales", 40);
>     someone.~person();  //此時會輸出一次"I'm deleting Wales with age 40"
> 
>     return 0;  //第二次輸出"I'm deleting  with age 40"
> }
> /* 在這裡，程式結束時會自動調用析構函數，
>    而person.name在第一次調用析構函數時已被清除，
>    但person.age會按編譯器而定，
>    沒能在第一次調用析構函數時清零。 */
> ```

* finalize()方法：_**protected**_ void finalize() throws _**Throwable**_
  * 在对象回收时就算抛出了任何的异常，也不会影响到整个程序的正常执行。

```java
package com.alpha;
class Message {
	public Message() {
		System.out.println("Constructor");
	}
	@Override
	protected void finalize() throws Throwable {
		System.out.println("Destructor");
		throw new Exception("do something");
	}
}
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Message message = new Message();
		message = null;
		System.gc();
	}
}
```

构造方法是留给对象初始化时使用的，而finalize()方法是留给对象回收前使用的。

**面试题：**请解释final、finally、finalize的区别？

* final：关键字，定义不能被继承的类、不能被覆写的方法、常量；
* finally：关键字，异常的统一出口；
* finalize()：方法，Object类提供的方法（protected void finalize() throws Throwable），指的是对象回收前的收尾方法即使出现了异常也不回导致程序中断执行。

#### 总结

1、System类可以使用currentTimeMillies()方法取得当前的系统时间；

2、System类中的gc()方法直接调用了“Runtime.getRuntime().gc()”。

### 对象克隆

1、清楚对象克隆的操作结构；

2、巩固接口的作用。

对象克隆指的就是对象的复制操作，在Object类里面提供有一个专门的克隆方法。

* 对象克隆：_**protected**_ Object clone() throws _**CloneNotSupportedException**_

此方法上抛出一个“CloneNotSupportedException”异常，如果要使用对象克隆的类没有实现Cloneable接口，那么就会抛出此异常。但是Cloneable接口看不见方法，此为表示接口，表示一种操作能力。

> 查阅Java8开发文档：
> 
> CloneNotSupportedException - if the object's class does not support the Cloneable interface. Subclasses that override the clone method can also throw this exception to indicate that an instance cannot be cloned.

```java
package com.alpha;
class Book implements Cloneable { // 此类的对象可以被克隆
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]";
	}
	// 由于此类需要对象的克隆操作，所以才需要进行方法的覆写
	@Override
	public Object clone() throws CloneNotSupportedException {
		return super.clone(); // 调用父类的克隆方法
	}
}
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Book booka = new Book("Java", 15.5);
		Book bookb = (Book) booka.clone();
		bookb.setTitle("C++");
		System.out.println(booka);
		System.out.println(bookb);
	}
}
```

对象克隆的操作，理论价值大于实际价值，因为在实际的工作里面很少会用到对象克隆的操作。重点在于标识接口，以后仍然会见到没有方法的接口，这样的接口就好比通行证一样，标识的是能力。

#### 总结

标识接口，没有任何方法定义，只是一个空接口的声明。

```java
```

