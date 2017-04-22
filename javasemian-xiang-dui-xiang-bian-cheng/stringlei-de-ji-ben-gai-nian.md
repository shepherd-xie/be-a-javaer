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

