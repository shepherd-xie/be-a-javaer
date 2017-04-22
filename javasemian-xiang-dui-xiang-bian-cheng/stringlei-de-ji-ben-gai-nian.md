## String类的基本概念

---

String是一个字符串类型的类，使用“ " ”定义的内容都是字符串，但是String本身毕竟是一个类，虽然这个类在使用上会有一些特殊，但是我们必须从类的角度与内存关系上来分析这个类的作用。

### String类的两种实例化方式

在之前使用过String，最早的时候是直接采用了“String 变量 = "字符串";”语法形式完成的。这种形式称为直接赋值。

```java
public class MainClass {
    public static void main(String[] args) {
        String str = "Hello World !";    //直接赋值
        System.out.println(str);
    }
}
```

以上就是String对象的直接赋值，代码并没有使用关键字new进行内存空间的开辟。在String类里面实际上也定义有一个构造方法：

* 构造方法：public String\(String str\)，在构造里面依然要接收一个本类对象；

**范例：**利用构造方法实例化

```java
public class MainClass {
    public static void main(String[] args) {
        String str = new String("Hello World !");
        System.out.println(str);
    }
}
```

String类有两种形式，主观上会认为第二种构造方法的形式更加适合我们，因为只要是类就应该使用new来创造对象，这样的做法似乎是最合理的。

### 字符串的比较

如果现在有两个int型整数想要判断其是否相等，可以使用“ == ”符号进行判断。

**范例：**判断两个int型整数是否相等

```java
public class MainClass {
    public static void main(String[] args) {
        int x = 10;
        int y = 10;
        System.out.println(x == y);
    }
}
```

但是需要知道的是在String上也可以使用“ == ”比较，那么比较的结果如何呢？

**范例：**在String中使用“ == ”判断

```java
public class MainClass {
    public static void main(String[] args) {
        String stra = "hello";
        String strb = new String("hello");
        String strc = strb;    //引用传递
        System.out.println(stra == strb);    //false
        System.out.println(stra == strc);    //false
        System.out.println(strb == strc);    //true
    }
}
```

以上三个String类对象的内容完全一样，既然完全一样，那么结果应该是true，可实际的代码显示这样的结果并不是正确的。下边通过内存关系进行分析。

![](/assets/import 2.4-1.png)通过以上的分析结果可以发现，“ == ”现在确实是进行了比较，但是比较的并不是字符串包含的内容，而是字符串实例对象所在的内存地址的数值，所以“ == ”是属于数值比较，比较的是内存地址。

如果想要去比较字符串的内容，那么可以使用String类里面定义的方法：

* 比较内容：public boolean equals\(String str\);

**范例：**实现内容比较

```java
public class MainClass {
	public static void main(String[] args) {
		String stra = "hello";
		String strb = new String("hello");
		String strc = strb;	//引用传递
		System.out.println(stra.equals(strb));	//true
		System.out.println(stra.equals(strc));	//true
		System.out.println(strb.equals(strc));	//true
	}
}
```

此时实现了字符串内容的比较，所以在以后的开发之中，只要是进行字符串的相等判断，千万不要使用“ == ”完成。

**面试题：**请解释在字符串相等的判断中“ == ”与“ equals\(\)”的区别？

* “ == ”是Java提供的关系运算符，主要的功能是进行数值相等判断的，如果用在了String对象上表示的是内存地址数值的比较；
* “ equals\(\) ”是String提供的一个方法，由于进行字符串内容的比较。



