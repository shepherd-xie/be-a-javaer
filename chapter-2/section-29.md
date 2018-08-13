## Java8新特性

* [接口定义增强](chapter-2/section-29.md#接口定义增强)
* [Lambda表达式](chapter-2/section-29.md#Lambda表达式)
* [方法引用](chapter-2/section-29.md#方法引用)
* [函数式接口](chapter-2/section-29.md#函数式接口)

---

### 接口定义增强

使用default与static定义接口方法。

从Java发展之初到今天已经经过20年的时间了，在这20年的时间里面所有的开发者都知道Java的接口由全局常量和抽象方法所组成。但是从JDK1.8的时代这一组成改变了。

如果说现在有某一个接口，这个接口随着时间的发展已经产生了2W个子类。突然有一天发现，这个接口里面的方法不足，应该再增加一个方法，而针对于所有不同的子类，这个方法的功能实现是完全相同的。按照最初的做法，应该在每一个子类上都覆写这个新的方法。那么就要修改2W个子类。

所以为了解决这样的问题，允许在接口里面定义普通方法。但是如果要定义普通方法就必须明确的使用default来进行定义。

```java
package com.alpha;
interface IMessage { // 定义接口
	public void print(); // 这是一个接口里面原本定义的方法
	default void fun() { // 在接口中定义了一个普通的方法
		System.out.println("毁三观的方法出现了！");
	}
}
class MessageImpl implements IMessage {
	@Override
	public void print() {
		System.out.println("Hello World!");
	}
}
public class MainClass {
	public static void main(String[] args) {
		IMessage msg = new MessageImpl();
		msg.fun(); // 此方法是在接口里面直接定义的
	}
}
```

除了使用default定义方法之外，还可以使用static定义方法，一旦使用了static定义方法意味着这个方法可以直接由类名称调用。

**范例：**定义static方法

```java
package com.alpha;
interface IMessage { // 定义接口
	public void print(); // 这是一个接口里面原本定义的方法
	default void fun() { // 在接口中定义了一个普通的方法
		System.out.println("毁三观的方法出现了！");
	}
	static void get() {
		System.out.println("直接由接口调用！");
	}
}
class MessageImpl implements IMessage {
	@Override
	public void print() {
		System.out.println("Hello World!");
	}
}
public class MainClass {
	public static void main(String[] args) {
		IMessage msg = new MessageImpl();
		msg.fun(); // 此方法是在接口里面直接定义的
		IMessage.get();
	}
}
```

在JDK1.8里面有一个最重要的概念：内部类访问方法参数的时候可以不加final关键字。

所有出现的这些新特性，完全到了Java已有的代码组成形式。

#### 总结

1、接口里面使用default或static定义方法的意义是避免子类重复实现同样的代码；

2、接口的使用还应该以抽象方法为主。

### Lambda表达式

1、分析函数式编程的产生原因；

2、掌握函数式编程的语法。

Lambda属于函数式编程的概念，那么为什么需要函数式的编程呢？

> **函数式编程**（英语：**functional programming**）或称函数程序设计，又称泛函编程，是一种编程范型，它将电脑运算视为数学上的函数计算，并且避免使用程序状态以及易变对象。函数编程语言最重要的基础是λ演算（lambda calculus）。而且λ演算的函数可以接受函数当作输入（引数）和输出（传出值）。
> 比起命令式编程，函数式编程更加强调程序执行的结果而非执行的过程，倡导利用若干简单的执行单元让计算结果不断渐进，逐层推导复杂的运算，而不是设计一个复杂的执行过程。

如果想要清楚函数式编程的目的，那么必须用过匿名内部类来分析。

**范例：**传统的匿名内部类

```java
package com.alpha;
interface IMessage { 
	public void print();
}
public class MainClass {
	public static void main(String[] args) {
		fun(new IMessage() {
			@Override
			public void print() {
				System.out.println("Hello World!");
			}
		});
	}
	public static void fun(IMessage msg) {
		msg.print();
	}
}
```

实际上整个代码之中，如果是fun()方法，最终需要的只是一个输出而已，但是由于Java的开发的结构性完整要求，所以不得不在这个核心的语句上嵌套更多的内容。

以上的做法要求的实在是过于严谨了，所以在JDK1.8时代引入了函数式编程，可以简化以上的代码。

**范例：**使用Lambda表达式

```java
package com.alpha;
interface IMessage { 
	public void print();
}
public class MainClass {
	public static void main(String[] args) {
		fun(() -> System.out.println("Hello World!"));
	}
	public static void fun(IMessage msg) {
		msg.print();
	}
}
```

整个操作里面匿名内部类只是进行一行语句的输出，所以此时使用了Lambda表达式可以非常轻松的实现输出要求。

对于Lambda语法有三种形式：

* (参数) -> 单行语句；
* (参数) -> {多行语句}；
* (参数) -> 表达式。

**范例：**观察有参数的单行语句

```java
package com.alpha;
interface IMessage { 
	public void print(String str);
}
public class MainClass {
	public static void main(String[] args) {
		// 首先要定义出此表达式里面需要接受变量，但行语句直接进行输出
		fun((s) -> System.out.println(s));
	}
	public static void fun(IMessage msg) {
		msg.print("Hello World!"); // 设置参数的内容
	}
}
```

**范例：**编写多行语句

```java
package com.alpha;
interface IMessage { 
	public void print(String str);
}
public class MainClass {
	public static void main(String[] args) {
		// 首先要定义出此表达式里面需要接受变量，但行语句直接进行输出
		fun((s) -> {
			s = s.toUpperCase();
			System.out.println(s);
		});
	}
	public static void fun(IMessage msg) {
		msg.print("Hello World!"); // 设置参数的内容
	}
}
```

如果说现在代码里面只是一个简单的计算表达式，那么操作也可以很容易。

**范例：**编写一个表达式

```java
package com.alpha;
interface IMessage { 
	public int add(int x, int y);
}
public class MainClass {
	public static void main(String[] args) {
		// 首先要定义出此表达式里面需要接受变量，但行语句直接进行输出
		fun((s1, s2) -> s1 + s2);
	}
	public static void fun(IMessage msg) {
		System.out.println(msg.add(10, 20));
	}
}
```

```java
	fun((s1, s2) -> {return s1 + s2;});
```

如果现在只是一个表达式，那么进行操作的返回，还是不写return比较合适，是多行的时候才可以考虑写上return。

#### 总结

利用Lambda表达式最终解决的问题：避免了匿名内部类定义过多无用的操作。

### 方法引用

掌握四种方法引用的使用。

一直以来都只是在对象上能发现引用的身影，而对象引用的特点：不同的对象可以操作统一块内容。而所谓的方法引用就是指为一个方法设置别名，相当于一个方法定义了不同的名字。

方法引用在Java8之中一共定义了四种形式：

* 引用静态方法：类名称 :: static方法名称；
* 引用某个对象的方法：实例化对象 :: 普通方法；
* 引用特定类型的方法：特定类 :: 普通方法；
* 引用构造方法：类名称 ::new。

**范例：**引用静态方法

* 在String类里面有一个valueOf()方法：public static String valueOf(int x);

```java
package com.alpha;
/**
 * 实现方法的引用接口
 * @param <P> 引用方法的参数类型
 * @param <R> 引用方法的返回类型
 */
interface IMessage<P, R> { 
	public R conversion(P p);
}
public class MainClass {
	public static void main(String[] args) {
		// 即：将String.valueOf()方法变为了IMessage接口里的conversion()方法
		IMessage<Integer, String> msg = String :: valueOf;
		String str = msg.conversion(1000);
		System.out.println(str.replaceAll("0", "9"));
	}
}
```

**范例：**普通方法引用

```java
package com.alpha;
/**
 * 实现方法的引用接口
 * @param <P> 引用方法的参数类型
 * @param <R> 引用方法的返回类型
 */
interface IMessage<R> { 
	public R upper();
}
public class MainClass {
	public static void main(String[] args) {
		// String类的toUpperCase()定义：public String toUpperCase()
		// 这个方法没有参数，但是有返回值，并且这个方法一定要在有实例化对象的情况下才可以调用
		// "hello"字符串是String类的实例化对象，所以可以直接调用toUpperCase()方法
		// 将toUpperCase()函数的引用交给了IMessage接口
		IMessage<String> msg = "hello" :: toUpperCase;
		String str = msg.upper(); // 相当于“"hello".toUpperCase()”
		System.out.println(str );
	}
}
```

通过两个代码演示应该已经发现了，如果要实现函数的引用，那么必须要有接口，而且最为关心的是，接口里面需要只存在一个方法。方法是无法进行引用的。

所以为了保证被引用接口里面只能够有一个方法，那么就需要增加一个注解的声明。

```java
/**
 * 实现方法的引用接口
 * @param <P> 引用方法的参数类型
 * @param <R> 引用方法的返回类型
 */
@FunctionalInterface // 此为函数式接口，只能定义一个方法
interface IMessage<R> { 
	public R upper();
}
```

在进行方法引用的过程里面还有另外一种形式的引用（他需要特定类的对象支持），正常情况下如果使用了“类 :: 方法”，引用的一定是类中的静态方法，但是这种形式也可以引用普通方法。

例如：在String类里里面有一个方法：public static int compareTo(String anotherString)

如果要进行比较的话，比较的形式：字符串1对象.compareTo(字符串2对象)，也就是说如果真要引用这个方法就需要准备出两个参数。

**范例：**引用特定类的方法

```java
package com.alpha;
@FunctionalInterface // 此为函数式接口，只能定义一个方法
interface IMessage<P> { 
	public int compare(P p1, P p2);
}
public class MainClass {
	public static void main(String[] args) {
		IMessage<String> msg = String :: compareTo;
		System.out.println(msg.compare("A", "B"));
	}
}
```

与之前相比，方法引用前不在需要定义对象，而是可以理解为将对象定义在了参数上。

**范例：**引用构造方法

```java
package com.alpha;
@FunctionalInterface // 此为函数式接口，只能定义一个方法
interface IMessage<C> { 
	public C create(String t, double p);
}
class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]";
	}
}
public class MainClass {
	public static void main(String[] args) {
		IMessage<Book> msg = Book :: new; // 引用构造方法
		// 调用的虽然是create()，但是这个方法引用的是Book类的构造
		Book book = msg.create("Java开发", 20.2);
		System.out.println(book);
	}
}
```

对象的引用是使用不同的名字，而方法引用是需要有一个函数式接口，并且要设置好参数。

#### 总结

四种方法引用基本的形式如上，但是是否真的去使用，现在给你的答案是：NO。

### 函数式接口

观察在JDK1.8中提供的新的函数式接口包以及提供的四个函数式接口

对于方法的引用，严格来讲都需要定义一个接口，可是不管如何操作，实际上有可能操作的接口只有四种，在JDK1.8里面提供了一个包：java.util.function，提供有以下四个核心接口：

1、功能性接口（Function）：public interface Function<T, R> {public R apply(T t);}

* 此接口需要接受一个参数，并且返回一个处理结果；

2、消费型接口（Consumer）：public interface Consumer<T> {public void accept(T t);}

* 此接口只是负责接收数据（引用数据是不需要返回），并且不返回处理结果；

3、供给型接口（Supplier）：public interface Supplier<T> {public T ger();}

* 此接口不接收参数，但是可以返回结果；

4、断言型接口（Predicate）：public interface Predicate<T> {public boolean test(T t);}

* 进行判断操作使用；

所有在JDK1.8之中由于存在有以上的四种功能型接口，所以一般很少会由用户去定义新的函数式接口。

**范例：**观察函数式接口 —— 接收参数并且返回一个处理结果；

* String类有一个方法：public boolean startWith(String str)

```java
package com.alpha;
import java.util.function.Function;
public class MainClass {
	public static void main(String[] args) {
		Function<String, Boolean> fun = "##hello" :: startsWith;
		System.out.println(fun.apply("##"));
	}
}
```

**范例：**消费型接口

```java
package com.alpha;
import java.util.function.Consumer;
class MyDemo {
	public void print(String str) { // 此方法没有返回值，但是有参数
		System.out.println(str);
	}
}
public class MainClass {
	public static void main(String[] args) {
		Consumer<String> cons = new MyDemo() :: print;
		cons.accept("Hello World!");
	}
}
```

```java
public class MainClass {
	public static void main(String[] args) {
		Consumer<String> cons = System.out :: println;
		cons.accept("Hello World!");
	}
}
```

**范例：**供给型接口

* 引用String类的toUpperCase()方法：public String toUpperCase();

```java
package com.alpha;
import java.util.function.Supplier;
public class MainClass {
	public static void main(String[] args) {
		Supplier<String> sup = "hello" :: toUpperCase;
		System.out.println(sup.get());
	}
}
```

**范例：**断言型接口

* String类里面有一个equalsIgnoreCase()方法

```java
package com.alpha;
import java.util.function.Predicate;
public class MainClass {
	public static void main(String[] args) {
		Predicate<String> pre = "hello" :: equalsIgnoreCase;
		System.out.println(pre.test("Hello"));
	}
}
```

这几个接口包含了所有可能出现的方法引用，也是函数式接口的代表，但是有许多的接口与它类似。

#### 总结

有了这几个函数式接口，那么开发中就不再需要了。

所有讲解的这一切都要为最后的数据流准备。

