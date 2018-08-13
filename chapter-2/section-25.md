## Java5新特性

* [可变参数](chapter-2/section-25.md#可变参数)
* [foreach循环](chapter-2/section-25.md#foreach循环)
* [静态导入](chapter-2/section-25.md#静态导入)

---

从JDK1.5开始，Java的开发就出现了许多的新特性。

### 可变参数

现在需要设计一个方法，这个方法可以接受任意多个整形数据的相加。最早的设计是利用数组来解决这个问题。

**范例：**最初的解决方案

```java
public class MainClass {
	public static void main(String[] args) {
		System.out.println(add(new int[]{1, 2, 3})); // 传递3个整形数据
		System.out.println(add(new int[]{10, 20})); // 传递2个整形数据
	}
	/**
	 * 实现任意多个整形数据的相加操作
	 * @param data 由于要接受多个整形数据，所以使用数组完成接受
	 * @return 多个整形数据的累加结果
	 */
	public static int add(int[] data) {
		int sum = 0;
		for (int i = 0; i < data.length; i ++) {
			sum += data[i];
		}
		return sum;
	}
}
```

以上的代码之所以使用数组，是因为多个参数方法上无法描述，所以利用数组整个多个参数，但是严格来讲这样的实现并不标准。要求是可以接受任意多个整型数据：

理想的调用形式：add(1, 2, 3)、add(10, 20)、add(10, 10, 10, 20, 20, 20, 30, ...)；

这一功能从JDK1.5之后正式的登录到了Java之中，它主要是在方法上使用，其定义形式：

```
[public | propected | private] [static] [final] [abstract] 返回值类型 方法类型(参数类型... 变量) {
	[return[返回值] ;]
}
```

此时给出的参数不再是一个内容，而是多个内容，但是尽管参数的定义形式变了，但是参数的访问却没有改变，也就是说在进行参数访问的时候按照数组的形式操作。

```java
public class MainClass {
	public static void main(String[] args) {
		// 可变参数支持接受数组
		System.out.println(add(new int[]{1, 2, 3})); // 传递3个整形数据
		System.out.println(add(new int[]{10, 20})); // 传递2个整形数据
		// 或者使用“,”区分不同的参数，接受的时候还是数组
		System.out.println(add(1, 2, 3)); // 传递3个参数
		System.out.println(add(10, 20)); // 传递2个参数
		System.out.println(add()); // 不传递参数
	}
	/**
	 * 实现任意多个整形数据的相加操作
	 * @param data 由于要接受多个整形数据，所以使用数组完成接受
	 * @return 多个整形数据的累加结果
	 */
	public static int add(int... data) {
		int sum = 0;
		for (int i = 0; i < data.length; i ++) {
			sum += data[i];
		}
		return sum;
	}
}
```

在大部分的开发情况下，应该要求参数的个数是准确的，所以对于这样的开发往往不会用于应用型的开发上，可能用于一些程序相关系统类的设计使用上。

#### 总结

1、在设计一个类的时候可变参数绝对不是有限的选择；

2、可变参数就属于数组的变形应用。

## foreach循环

增强型for循环的使用。

foreach输出是由C#最早引入的概念。其目的就是进行数组或者是集合数据的输出。

在最早如果要进行数组输出肯定使用for循环，而后利用下标进行数据的访问。

```java
public class MainClass {
	public static void main(String[] args) {
		int data[] = new int[] {1, 2, 3, 4, 5};
		for (int i = 0; i < data.length; i ++) {
			System.out.println(data[i]);
		}
	}
}
```

有人会认为以上的输出需要使用索引会比较麻烦。

从JDK1.5之后增加的foreach循环形式就可以取消掉索引的操作形式。语法如下：

```java
for (类型 变量 : 数组 | 集合) {
	// 每次循环会自动的将数组的内容设置给变量
}
```

**范例：**观察增强型for循环

```java
public class MainClass {
	public static void main(String[] args) {
		int data[] = new int[] {1, 2, 3, 4, 5};
		for (int i : data) { 
			System.out.println(i);
		}
	}
}
```

用数组直接通过索引访问会比较麻烦。而有了这样的形式代码就避免了索引的麻烦。

### 静态导入

如果说现在某一个类中定义的方法全部都属于static型的方法，那么其他类要引用此类时必须使用“类名称.方法()”进行调用。

**范例：**传统的做法

```java
package com.alpha.util;
public class MyMath {
	public static int add(int x, int y) {
		return x + y;
	}
	public static int div(int x, int y) {
		return x / y;
	} 
}
```

此时MyMath类里面的方法都是static型的方法，随后在其他类使用这些方法。

**范例：**基本使用形式

```java
package com.alpha;
import com.alpha.util.MyMath;
public class MainClass {
	public static void main(String[] args) {
		System.out.println("加法操作：" + MyMath.add(10, 20));
		System.out.println("除法操作：" + MyMath.div(10, 20));
	}
}
```

如果在主类中定义的是static方法，那么可以直接调用static方法，而现在的MyMath类里面都是static方法，那么觉得前面加上类名称实在是多余。于是从JDK1.5开始增加了静态导入。

**范例：**静态导入

```java
package com.alpha;
// 将MyMath类中的全部static方法导入，这些方法就好比在主类中定义的static方法一样
import static com.alpha.util.MyMath.*;
public class MainClass {
	public static void main(String[] args) {
		// 直接使用方法名访问
		System.out.println("加法操作：" + add(10, 20));
		System.out.println("除法操作：" + div(10, 20));
	}
}
```

从道理上来讲，如果在前面加上了类名称，反而能够清楚的表示出方法具体出自那个类。

_静态导入清楚内容就可以了，实际上意义并不大。_

