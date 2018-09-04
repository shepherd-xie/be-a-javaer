## 异常的捕获及处理

---

1、异常的产生以及对于程序的影响；

2、异常处理的格式；

3、异常的处理流程；

4、throw、throws关键字的使用；

5、异常处理的使用标准；

6、自定义异常。

异常是Java的一个重大特色，合理使用异常处理，可以让我们程序更加的健壮。

### 异常的产生

异常是导致程序中断执行的一种指令流，异常一旦出现并且没有进行合理处理的话，那么程序就将中断执行。

**范例：** 不产生异常的代码

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		System.out.println("2、除法计算：" + (10 / 2));
		System.out.println("3、除法计算结束。");
	}
}
```

```
1、除法计算开始。
2、除法计算：5
3、除法计算结束。
```

**范例：** 产生异常

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		System.out.println("2、除法计算：" + (10 / 0)); // 此处产生异常
		System.out.println("3、除法计算结束。");
	}
}
```

```java
1、除法计算开始。
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at MainClass.main(MainClass.java:4)
```

一旦产生异常之后发现产生异常的语句以及之后的语句将不再执行，默认情况下是进行异常信息的输出，而后自动结束程序的执行。

我们要做的事情是：即使出现了异常，那么也应该让程序正确的执行完毕。

### 处理异常

如果想要进行异常的处理，在Java之中提供了三个关键字：try、catch、finally，这三个关键字的使用语法如下：

```java
try {
	// 有可能出现异常的语句
} [ catch (异常类型 对象) {
	// 处理异常
} catch (异常类型 对象) {
	// 处理异常
} catch (异常类型 对象) {
	// 处理异常
} ... ] [ finally {
	// 不管是否出现异常，都执行的统一代码
}]
```

对于以上的操作组合：try...catch、try...catch...finally、try...finally。

**范例：** 应用异常处理格式

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		try {
			System.out.println("2、除法计算：" + (10 / 0)); // 此处产生异常
		} catch (ArithmeticException e) {
			System.out.println("******** 出现异常 ********");
		}
		System.out.println("3、除法计算结束。");
	}
}
```

```
1、除法计算开始。
******** 出现异常 ********
3、除法计算结束。
```

由于使用了异常处理，这样即使程序出现了异常，发现也可以正常的执行完毕。

出现异常的目的是为了解决异常，所以为了能够进行异常的处理，可以使用异常类中提供的printStackTrace()方法进行异常信息的完整输出。

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		try {
			System.out.println("2、除法计算：" + (10 / 0)); // 此处产生异常
		} catch (ArithmeticException e) {
			e.printStackTrace();
		}
		System.out.println("3、除法计算结束。");
	}
}
```

```
1、除法计算开始。
java.lang.ArithmeticException: / by zero
	at MainClass.main(MainClass.java:5)
3、除法计算结束。
```

此时发现打印的异常信息是很完整的。

**范例：** 使用try...catch...finally

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		try {
			System.out.println("2、除法计算：" + (10 / 0)); // 此处产生异常
		} catch (ArithmeticException e) {
			e.printStackTrace();
		} finally {
			System.out.println("不管是否出现异常都会执行");
		}
		System.out.println("3、除法计算结束。");
	}
}
```

在异常捕获的时候发现一个try语句后面可以跟着多个catch语句。

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		try {
			int x = Integer.parseInt(args[0]);
			int y = Integer.parseInt(args[1]);
			System.out.println("2、除法计算：" + (x / y)); // 此处产生异常
		} catch (ArithmeticException e) {
			e.printStackTrace();
		} finally {
			System.out.println("不管是否出现异常都会执行");
		}
		System.out.println("3、除法计算结束。");
	}
}
```

以上的程序将由用户输入操作数据，于是可能存在有如下的情况出现：

* 用户执行时不输入参数（java MainClass）：java.lang.ArrayIndexOutOfBoundsException，没有处理；
* 用户输入的参数不是数字（java MainClass a b）：java.lang.NumberFormatException，没有处理;
* 被除数为0（java MainClass 10 0）：java.lang.ArithmeticException，已处理。

以上的代码里面只存在有一个catch，所以只能够处理一个异常，如果其他没有处理的异常，依然会导致程序中断执行。

**范例：** 加入多个catch

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		try {
			int x = Integer.parseInt(args[0]);
			int y = Integer.parseInt(args[1]);
			System.out.println("2、除法计算：" + (x / y)); // 此处产生异常
		} catch (ArrayIndexOutOfBoundsException e) {
			e.printStackTrace();
		} catch (NumberFormatException e) {
			e.printStackTrace();
		} catch (ArithmeticException e) {
			e.printStackTrace();
		} finally {
			System.out.println("不管是否出现异常都会执行");
		}
		System.out.println("3、除法计算结束。");
	}
}
```

### 异常的处理流程

通过以上的分析应该已经掌握了异常的处理格式，但是以上的操作并不是最好的异常处理方式，所以必须要清楚整个Java中的异常的处理流程。

首先来观察两个异常类的继承结构：

<table>
<thead>
<td>ArithmeticException</td>
<td>NumberFormatException</td>
</thead>
<tbody>
<td>
|- java.lang.Object <br />
|-- java.lang.Throwable <br />
|--- java.lang.Exception <br />
|---- java.lang.RuntimeException <br />
|----- java.lang.ArithmeticException <br />
</td>
<td>
|- java.lang.Object <br />
|-- java.lang.Throwable <br />
|--- java.lang.Exception <br />
|---- java.lang.RuntimeException <br />
|----- java.lang.IllegalArgumentException <br />
|------ java.lang.NumberFormatException <br />
</td>
<tbody>
</table>

经过异常类的观察可以发现所有的异常类都是Throwable的子类。而在Throwable下有两个子类。

（面试题：请解释Error和Exception的区别？）

* Error：指的是JVM错误，即：此时的程序还没有执行，如果没有执行用户无法处理；
* Exception：指的是程序运行中产生的异常，用户可以处理。

也就是说所谓的异常处理指的就是所有的Exception以及它的子类异常。

**面试题：**请解释Java中的异常处理流程

![](/images/chapter-2/section-24/1.png)

1、当程序在运行过程之中出现了异常后，会由JVM自动根据异常的类型实例化一个与之类型匹配的异常类对象（此处用户不用去关心new，由系统自动负责处理）；

2、产生了异常对象之后会判断当前的语句上是否存在有异常处理，如果没有异常处理，那么就交给JVM进行默认的异常处理，处理的方式：输出异常信息，而后结束程序的调用；

3、如果此时存在有异常的捕获操作，那么会由try语句来捕获产生的异常类实例化对象，而后与try语句的每一个catch进行比较，如果有符合的捕获类型，则使用当前catch的语句来进行异常的处理，如果不匹配，则向下继续匹配其他的catch；

4、不管最后异常处理是否能够匹配，程序都要向后执行，如果此时程序种存在有finally语句，那么就先执行finally中的代码，但是执行完毕后需要根据之前的catch匹配结果来决定如何执行，如果之前已经成功的捕获了异常，那么就继续执行finally之后的代码，如果之前没有成功的捕获异常，那么就将此异常交给JVM进行默认处理（输出异常信息，输出异常然后结束程序执行）。

整个过程就好比方法重载一样。根据catch后面的参数类型进行匹配，但是所有的Java对象都存在有自动向上转型的特性，也就是说匹配所有的异常类，简单的做法是匹配Exception就够了。

**范例：** 使用Exception处理异常

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println("1、除法计算开始。");
		try {
			int x = Integer.parseInt(args[0]);
			int y = Integer.parseInt(args[1]);
			System.out.println("2、除法计算：" + (x / y)); // 此处产生异常
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			System.out.println("不管是否出现异常都会执行");
		}
		System.out.println("3、除法计算结束。");
	}
}
```

此时所有的异常都使用了Exception进行处理，所有在程序之中不用再去关心到底使用哪一个异常。

**两点说明：**

* 在编写多个catch捕获异常的时候，捕获范围大的异常一定要放在捕获范围小的异常之后，否则会产生编译错误；
* 虽然直接捕获Exception比较方便，但是这样也不好，因为所有的异常都会按照同一种方式进行处理，如果在一些要求严格的项目里面，异常一定要分开处理更好。

### throws关键字

throws关键字主要用于方法声明上，指的是当方法之中出现异常后交由被调用处来进行处理。

**范例：** 使用throws

```java
class MyMath {
	// 存在有throws，表示此方法里面产生的异常交给被调用处处理
	public static int div(int x, int y) throws Exception {
		return x / y;
	}
}
```

**范例：** 调用以上方法

```java
public class MainClass {
	public static void main(String[] args) {
		try {
			System.out.println(MyMath.div(10, 2));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

也就是说调用了具有throws声明的方法之后，那么不管操作是否出现异常，都必须使用try...catch来进行异常处理。

既然如此，主方法也属于方法，那么是否能在主方法上继续使用throws抛出异常呢？

```java
class MyMath {
	// 存在有throws，表示此方法里面产生的异常交给被调用处处理
	public static int div(int x, int y) throws Exception {
		return x / y;
	}
}
public class MainClass {
	public static void main(String[] args) throws Exception {
		// 表示异常产生之后直接通过主方法抛出
		System.out.println(MyMath.div(10, 0));
	}
}
```

在主方法上如果继续抛出了异常，那么这个异常就将交给JVM进行处理也就是说采用默认的处理方式，输出异常信息而后结束程序调用。

主方法上最好不要加上throws，因为程序如果出错了，一般来说也希望程序可以正常的结束调用。

### throw关键字

在程序之中可以之季节使用throw手工的抛出一个异常类的实例化对象。

**范例：** 手工抛出异常

```java
public class MainClass {
	public static void main(String[] args) {
		try {
			throw new Exception("自定义异常");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

**面试题：**请解释throw和throws的区别？

* throw：指的是方法之中人为抛出一个异常类对象（这个异常类对象可能是自己实例化或者是抛出已存在的）；
* throws：在方法的声明上使用，表示此方法在调用时必须处理异常。

### 异常的使用格式

现在要求定义一个div()方法，要求，这个方法在进行计算之前打印提示信息，在计算结束完毕也打印提示信息，如果在计算之中产生了异常则交给被调用处进行处理。

**范例：** 首先给出代码不出错的情况

```java
class MyMath {
	public static int div(int x, int y) {
		int result = 0;
		System.out.println("*** 1、除法计算开始 ***");
		result = x / y;
		System.out.println("*** 2、除法计算结束 ***");
		return result;
	}
}
public class MainClass {
	public static void main(String[] args) {
		System.out.println(MyMath.div(10, 2));
	}
}
```

以上给出的除法操作不可能永远都正常的完成，所以应该进行一些合理的处理，首先如果某一个方法出现异常了必须交给调用处处理，那么应该在方法上使用throws抛出。

```java
class MyMath {
	// 此时表示div()方法上如果出现了异常交给调用处处理
	public static int div(int x, int y) throws Exception {
		int result = 0;
		System.out.println("*** 1、除法计算开始 ***");
		result = x / y;
		System.out.println("*** 2、除法计算结束 ***");
		return result;
	}
}
public class MainClass {
	public static void main(String[] args) {
		try {
			System.out.println(MyMath.div(10, 2));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

可是如果以上的代码真的出错了呢？程序有些内容就不执行了

```java
class MyMath {
	// 此时表示div()方法上如果出现了异常交给调用处处理
	public static int div(int x, int y) throws Exception {
		int result = 0;
		System.out.println("*** 1、除法计算开始 ***");
		try {
			result = x / y;
		} catch (Exception e) {
			throw e; // 继续抛异常
		} finally {
			System.out.println("*** 2、除法计算结束 ***");
		}
		return result;
	}
}
public class MainClass {
	public static void main(String[] args) {
		try {
			System.out.println(MyMath.div(10, 0));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

实际上以上的代码还可以缩写。

```java
class MyMath {
	// 此时表示div()方法上如果出现了异常交给调用处处理
	public static int div(int x, int y) throws Exception {
		int result = 0;
		System.out.println("*** 1、除法计算开始 ***");
		try {
			result = x / y;
		} finally {
			System.out.println("*** 2、除法计算结束 ***");
		}
		return result;
	}
}
public class MainClass {
	public static void main(String[] args) {
		try {
			System.out.println(MyMath.div(10, 0));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

不过不建议使用这种形式，还是提倡使用完整的try...catch...finally较为稳妥。

### RuntimeException类

下面首先来观察一个程序代码。

```java
public class MainClass {
	public static void main(String[] args) {
		int temp = Integer.parseInt("100");
	}
}
```

现在来观察一下parseInt()方法的定义。

* 方法：public static int parseInt(String s) throws NumberFormatException;

此时parseInt()方法上抛出了NumberFormatException，按照道理来讲，应该进行强制性的异常捕获，可是现在并没有这种强制性的要求，来观察一下NumberFormatException的继承结构：

```
|- java.lang.Object 
|-- java.lang.Throwable 
|--- java.lang.Exception 
|---- java.lang.RuntimeException  -> 运行时异常
|----- java.lang.IllegalArgumentException 
|------ java.lang.NumberFormatException 
```

在Java里面为了方便用户代码的编写，专门提供了一种RuntimeException类，这种异常类的最大特征在于：程序在编译的时候不会强制性的要求用户处理异常，用户可以根据自己的需要选择性进行处理，但是如果没有处理又发生异常了，将交给JVM默认处理。也就是说RuntimeException的子异常类，可以根据用户需要选择进行处理。RuntimeException又称_**非受检异常（Unchecked Exception）**_，RuntimeException以外的强制用户处理的异常又称_**受检异常（Checked Exception）**_。

**面试题：**请解释Exception与RuntimeException的区别？请列举出几个常见的RuntimeException；

* Exception是RuntimeException的父类；
* 使用Exception定义的异常必须要被处理，而RuntimeException的异常可以选择性处理；
* 常见的RuntimeException：ArithmeticException、NullPointerException、ClassCastException。

### assert关键字

assert关键字是在JDK1.4的时候引入的，其主要功能是进行断言。

在Java中的断言指的是程序执行到某行代码处时一定是预期的结果。

**范例：** 观察断言

```java
public class MainClass {
	public static void main(String[] args) {
		int num = 10;
		// 经过一些操作预期num的值为20
		assert num == 20 : "num的内容不是20";
		System.out.println(num);
	}
}
```

默认情况下断言是不影响程序的运行的，也就是说在Java解释程序的时候，断言是默认不起作用的。

启用断言：java -ea MainClass

```java
Exception in thread "main" java.lang.AssertionError: num的内容不是20
	at MainClass.main(MainClass.java:5)
```

在Java里面断言的设计要比C++强的很多，他不会影响到程序的执行，但是使用的意义不大。

### 自定义异常

Java本身已经提供了大量的异常，但是这些异常在实际的工作之中往往并不够取是使用，例如：当你在执行数据增加操作的时候，有可能会出现一些错误的数据，而这些错误的数据一旦出现就应该抛出异常，例如：AddException，但是这样的异常Java并没有，所以就需要由用户自己去开发一个自己的异常类。

如果想要开发自定义的异常类可以选择继承Exception或者是RuntimeException。

**范例：** 定义AddException

```java
class AddException extends Exception {
	public AddException(String msg) {
		super(msg);
	}
}
public class MainClass {
	public static void main(String[] args) {
		int num = 20;
		try {
			if (num > 10) { // 出现了错误，应该产生异常
				throw new AddException("数值传递的过大");
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

这种代码只能说是介绍自定义异常的形式，但是并不能说明自定义异常的实际使用。

#### 总结

1. Exception的父类是Throwable，但是在编写代码的时候尽量不要使用Throwable，因为Throwable下还包含了一个Error子类，我们能够处理的只有Exception子类；
2. 异常处理的标准格式：tyr、catch、finally、throw、throws；
3. RuntimeException与Exception的区别。
