## 打印流

---

* 打印流的实现原理；
* 打印流操作类的使用。

打印流属于整个Java开发过程中，非常重要的一个概念。

### 问题引出

现在已经清楚了InputStream和OutputStream两个类的基本作用，但是此时有一个小小的问题，如果要进行输出只能够使用OutputStream类完成，但是OutputStream类在输出上是否真的方便？

如果要使用OutputStream输出数据，假设要出输出的是String，需要将String变为字节数组后再输出，如果要输出的是int，需要将int变为字符串而后在变为字节数组输出，同样，如果是boolean呢？或者是double呢？

既然OutputStream类有缺陷，那么此时就需要一个类，这个类支持各种数据类型的输出。

```java
package com.alpha;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
class PrintUtil { // 专门实现输出操作
	private OutputStream output; // 输出只能够依靠OutputStream
	public PrintUtil(OutputStream output) {
		this.output = output;
	}
	public void print(int x) throws IOException {
		this.print(String.valueOf(x));
	}
	public void print(String x) throws IOException {
		this.output.write(x.getBytes());
	}
	public void print(double x) throws IOException {
		this.print(String.valueOf(x));
	}
	public void println(int x) throws IOException {
		this.println(String.valueOf(x));
	}
	public void println(String x) throws IOException {
		this.print(x.concat("\r\n"));
	}
	public void println(double x) throws IOException {
		this.println(String.valueOf(x));
	}
	public void close() throws IOException {
		this.output.close();
	}
}
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 表示向文件输出
		PrintUtil pu = new PrintUtil(new FileOutputStream(new File("d:" + File.separator + "my.txt")));
		pu.print("Hello ");
		pu.println("World");
		pu.println(1 + 1);
		pu.println(1.1 + 1.1);
		pu.close();
	}
}
```

至少如果从使用的角度上来看，有了PrintUtil类之后，输出数据方便许多。

### 打印流

为了解决输出数据时的功能不足，所以在java.io包里面有提供了一套专门用于输出数据的类：PrintStream（打印字节流）、PrintWriter（打印字符流）。

以PrintStream类为例，观察一下这个类的继承与构造。

* java.lang.Object
  * java.io.OutputStream
    * java.io.FilterOutputStream
      * java.io.PrintStream

Constructor：public PrintStream(OutputStream out)；

在PrintStream类里面提供有一系列的print()、println()方法，这些方法支持各种数据类型的输出，也就是说如果使用了PrintStream，那么就不会去使用write()方法了。

但是整个操作过程之中，发现虽然操作的形式不同了，但是本质依然是基于OutputStream类的方法完成的。那么这样的设计，在Java之中称为装饰设计模式。相当于将一个功能不足的操作类，通过某些类的包装，形成更好用的工具类。

> ### 装饰模式（Decorator）
> 
> 修饰模式，是面向对象编程领域中，一种动态地往一个类中添加新的行为的设计模式。就功能而言，修饰模式相比生成子类更为灵活，这样可以给某个对象而不是整个类添加一些功能。
> 
> 通过使用修饰模式，可以在运行时扩充一个类的功能。原理是：增加一个修饰类包裹原来的类，包裹的方式一般是通过在将原来的对象作为修饰类的构造函数的参数。装饰类实现新的功能，但是，在不需要用到新功能的地方，它可以直接调用原来的类中的方法。修饰类必须和原来的类有相同的接口。

```java
package com.alpha;
import java.io.File;
import java.io.FileOutputStream;
import java.io.PrintStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 表示向文件输出
		PrintStream pu = new PrintStream(new FileOutputStream(new File("d:" + File.separator + "my.txt")));
		pu.print("Hello ");
		pu.println("World");
		pu.println(1 + 1);
		pu.println(1.1 + 1.1);
		pu.close();
	}
}
```

在以后的开发过程之中，只要是由程序输出内容，都会采用打印流的模式完成，但是打印流依然需要OutputStream的支持。

### JDK1.5的改进（了解）

所有的输出数据都要求使用打印流完成，但是在JDK1.5之后考虑到市场因素，所以增加了一种新的输出，称为格式化输出：public PrintStream printf(String format, Object... args)。

如果需要格式化输出就需要一些标记：整数（%d）、字符串（%s）、小数（%m.nf）、字符（%c）描述。

**范例：** 格式化输出

```java
package com.alpha;
import java.io.File;
import java.io.FileOutputStream;
import java.io.PrintStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 表示向文件输出
		String name = "mori";
		int age = 19;
		double score = 59.485586821868387;
		PrintStream pu = new PrintStream(new FileOutputStream(new File("d:" + File.separator + "my.txt")));
		pu.printf("姓名：%s，年龄：%d，成绩：%5.2f", name, age, score);
		pu.close();
	}
}
```

在开发之中，几乎不会使用到此类输出，此类的输出只是作为使用特色而已。

在JDK1.5之后，String类也发生里一个变化，定义了一个新的功能：格式化字符串；

* 方法：public static String format(String format, Object... args)。

```java
package com.alpha;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 表示向文件输出
		String name = "mori";
		int age = 19;
		double score = 59.485586821868387;
		String str = String.format("姓名：%s，年龄：%d，成绩：%5.2f", name, age, score);
		System.out.println(str);
	}
}
```

#### 总结

如果在日后进行程序输出操作的话，一定要使用打印流进行操作。
