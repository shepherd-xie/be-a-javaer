## System类对IO的支持

---

学习完PrintStream（PrintWriter）类之后会发现里面的方法很熟悉。

了解System类之中对IO操作的支持。

在System类里面为了支持IO操作专门提供有三个常量：

* 错误输出：public static final PrintStream err；
* 输出到标准输出设备（显示器）：public static final PrintStream out；
* 从标准输入设备输入（键盘）：public static final InputStream in；

### 错误输出：System.err

System.err是PrintStream类对象，此对象专门负责进行错误信息的输出操作。

```java
package com.alpha;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		try {
			Integer.parseInt("abc");
		} catch (Exception e) {
			System.err.println(e);
			System.out.println(e);
		}
	}
}
```

严格来讲System.out和System.err的功能是完全一样的，之所以这样设计，主要的目的是希望err可以输出不让用户看见的错误，而out输出的是可以让用户看见的错误，但是现在基本上没人再去区分这两者的区别。

### 信息输出：System.out

System.out是在Java之中专门准备的支持品目输出信息的操作对象（此对象由系统负责实例化）。那么下面就可以利用System.out来实现一个简单的输出操作。

**范例：**利用OutputStream实现屏幕输出

```java
package com.alpha;
import java.io.OutputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		OutputStream out = System.out;
		out.write("Hello World".getBytes());
	}
}
```

另外，需要结合JDK1.8的函数式接口，它可以为消费性函数式接口做方法引用。

```java
package com.alpha;
import java.util.function.Consumer;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		Consumer<String> con = System.out :: println;
		con.accept("Hello World!");
	}
}
```

在Java系统里面，System.out是默认提供好的实例化对象，不在需要用户进行明确的实例化操作。

### 系统输入：System.in

在任何语言里面都有一种功能：键盘输入的操作。但是Java本身并没有直接提供，但是在System类里面有一个in的对象。此对象的类型是InputStream。

**范例：**实现键盘的数据输入

```java
package com.alpha;
import java.io.InputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		InputStream input = System.in;
		byte[] data = new byte[1024];
		System.out.print("请输入数据：");
		int len = input.read(data);
		System.out.println("输入数据为：" + new String(data, 0, len));
	}
}
```

与之前最大的不同只是更换了一个实例化对象。现在已经实现了键盘数据的输入操作，但是有一个问题出现了，此时输入的时候开辟了一个数组，那么如果说数组的容量小于输入的长度呢？

那么现在会发现一个非常严重的问题，操作数组长度的数据将不会被保存。实际上，如果在开发之中，你永远也无法知道用户科恩能够输入的数据是多少。所以这种设置输入长度的操作不可能使用。

```java
package com.alpha;
import java.io.InputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		InputStream input = System.in;
		StringBuffer sb = new StringBuffer();
		System.out.print("请输入数据：");
		int temp = 0;
		while ((temp = input.read()) != -1 && temp != '\n') {
			sb.append(Character.toChars(temp));
		}
		System.out.println("输入数据为：" + sb.toString());
	}
}
```

虽然以上的操作没有了长度限制，但是依然无法使用。输入中文的时候发现成乱码了。

#### 总结

System类中对于IO的各种操作实际上并不会过多的使用到，因为唯一可以使用到的只是System.out.println()之类的方法。
