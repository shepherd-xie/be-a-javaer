## static关键字

---

static在使用上可以声明属性、方法。

### static定义属性

在讲解具体的static作用之前，首先观察一个代码。

**范例：**定义程序

```java
class Book { // 描述的是同一个出版社的图书信息
    private String title;
    private double price;
    // 定义一个描述出版社的信息，考虑到方便操作暂不封装
    String pub = "清华大学出版社";
    public Book(String title, double price) {
        this.title = title;
        this.price = price;
    }
    public String getInfo() {
        return "title=" + title + ", price=" + price + ", pub=" + pub;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book ba = new Book("Java", 10.2);
        Book bb = new Book("Android", 11.2);
        Book bc = new Book("Oracle", 12.2);
        // 修改了一个属性的内容
        ba.pub = "北京大学出版社";
        System.out.println(ba.getInfo());
        System.out.println(bb.getInfo());
        System.out.println(bc.getInfo());
    }
}
```

![](/images/chapter-2/section-10/1.png)

通过内存关系可以发现此时代码的问题：既然这个类所有图书的出版社都是同一个，那么就没有必要让每个对象都各自占有重复的属性信息。

既然所有对象的pub内容应该是一样的，那么应该将其定义为一个共享的属性，即：所有对象将共享同一个pub属性，那么在这种情况下为了描述出共享的概念，可以使用static来定义属性。

**范例：**使用static定义属性

```java
class Book { // 描述的是同一个出版社的图书信息
    private String title;
    private double price;
    // 定义一个描述出版社的信息，考虑到方便操作暂不封装
    static String pub = "清华大学出版社";
    public Book(String title, double price) {
        this.title = title;
        this.price = price;
    }
    public String getInfo() {
        return "title=" + title + ", price=" + price + ", pub=" + pub;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book ba = new Book("Java", 10.2);
        Book bb = new Book("Android", 11.2);
        Book bc = new Book("Oracle", 12.2);
        // 修改了一个属性的内容
        ba.pub = "北京大学出版社";
        System.out.println(ba.getInfo());
        System.out.println(bb.getInfo());
        System.out.println(bc.getInfo());
    }
}
```

一旦在属性定义上使用了static之后，只要有一个对象修改了属性的内容，那么所有的对象的static属性内容都将一起进行修改，也就是说此时的pub属性就变为了一个公共属性。

![](/images/chapter-2/section-10/2.png)

static声明的属性与普通属性（非static属性）最大的区别在于保存的内容区域的不同。

既然static是一个公共属性的概念，那么如果只是简单的由一个对象去修改static属性的做法很不合适，那么最好的做法是由所有对象的公共的代表来进行访问，就是类，所以利用static定义的属性是可以由类名称直接调用的。

```java
Book.pub = "北京大学出版社";
```

此时Book类定义了pub这个static属性，所以可以利用“Book”类直接调用static的pub属性。

static属性与非static属性还有一个最大的区别，所有的非static属性必须产生实例化对象之后才可以访问，但是static属性不受实例化对象的控制，也就是说，在没有实例化对象产生的情况下，依然可以使用static属性。

```java
public class MainClass {
    public static void main(String[] args) {
        // 在没有实例化对象的情况下直接输出属性内容
        System.out.println(Book.pub);
        Book.pub = "北京大学出版社";
        System.out.println(Book.pub);
        Book ba = new Book("Java", 10.2);
        System.out.println(ba.getInfo());
    }
}
```

所以可以发现sataic定义属性的一个特征：虽然定义在类结构里面，但是并不受到对象的控制，是独立于类存在的。

**疑问？**什么时候使用static属性？什么时候不使用static属性？

在编写类的过程之中，所选择的首要的修饰符一定不是static（95%），如果需要描述出共享信息的时候再使用static（可以方便集体修改，可以不重复开辟内存空间）。

### static定义方法

static定义方法的时候也可以在没有实例化对象的时候利用类名称直接调用。

**范例：**使用static定义方法

```java
class Book { // 描述的是同一个出版社的图书信息
    private String title;
    private double price;
    // 定义一个描述出版社的信息
    private static String pub = "清华大学出版社";
    public Book(String title, double price) {
        this.title = title;
        this.price = price;
    }
    public static void setPub(String p) {
        pub = p;
    }
    public String getInfo() {
        return "title=" + title + ", price=" + price + ", pub=" + pub;
    }
}
public class MainClass {
    public static void main(String[] args) {
        // 在没有对象产生的时候进行调用
        Book.setPub("北京大学出版社");
        Book ba = new Book("Java", 10.2);
        Book bb = new Book("Android", 11.2);
        Book bc = new Book("Oracle", 12.2);
        System.out.println(ba.getInfo());
        System.out.println(bb.getInfo());
        System.out.println(bc.getInfo());
    }
}
```

发现static定义的属性和方法都不受到实例化对象的控制，也就是说属于独立的类的功能。

但是这个时候就会出现一个特别麻烦的问题：此时类中的方法就变成了两组：static方法、非static方法。两组方法间的访问也将受到限制：

* static方法不能够直接访问非static属性或者是方法，只能够调用static属性或方法；
* 非static方法可以访问static的属性或者是方法，不受任何的限制。

**分析：**为什么会存在以上的限制？

* 所有的非static定义的结构，必须在类已经明确的产生了实例化对象才会分配堆空间，才可以使用；
* 所有的static定义的结构，不受实例化对象的控制，即：可以在没有实例化对象的时候访问。

**解决问题：**

在最早讲解方法的时候曾经讲过：  
如果一个方法定义在了主类方法之中，并且由主方法直接进行调用的话，方法语法：

```java
public static 返回值类型 方法名称(参数类型 参数, ...) {
    [return [返回值];]
}
```

而后来到了编写类的时候，发现方法的定义格式改变（方法由对象调用）。

```java
public 返回值类型 方法名称(参数类型 参数, ...) {
    [return [返回值];]
}
```

**范例：**观察如下代码

```java
public class MainClass {
    public static void main(String[] args) {
        fun();
    }
    public static void fun() {
        System.out.println("Hello World!");
    }
}
```

如果此时fun\(\)方法上取消了static，那么就变为了一个非static方法。所有的非static方法必须由对象调用，也就是说此时static方法如果要想使用非static操作，必须产生对象后才可以调用。

```java
public class MainClass {
    public static void main(String[] args) {
        new MainClass().fun();
    }
    public void fun() {
        System.out.println("Hello World!");
    }
}
```

与属性定义规则一样，定义一个类的时候首先考虑的依然是非static方法，因为所有的类如果保存的信息多（有属性），那么每一个对象执行同一个方法的时候，就可以利用自己的属性实现方法的不同调用。

```java
class Book {
    private boolean flag;
    public Book(boolean flag) {
        this.flag = flag;
    }
    public void fun() {
        if (flag) {
            System.out.println("可以操作！");
        } else {
            System.out.println("不可以操作哦！");
        }
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book ba = new Book(true);
        Book bb = new Book(false);
        ba.fun();
        bb.fun();
    }
}
```

但如果说现在一个类里面没有任何的属性存在，只有方法，建议可以将所有的方法定义为static方法，这样就不用在每次调用的时候实例化对象。

**范例：**定义一个数学的加法操作

```java
class MyMath {
    public static int add(int x, int y) {
        return x + y;
    }
}
public class MainClass {
    public static void main(String[] args) {
        System.out.println(MyMath.add(10, 20));
    }
}
```

此时MyMath类没有属性，产生对象完全没有任何的意义，所以会使用static方法。

_**写一个类的时候优先考虑的一定是非static方法或非static变量（95%）。**_

### 主方法

下面来观察Java main方法的组成：public static void main\(String\[\] args\)

* public：主方法是任何程序的开始，所以这个方法对任何的操作都一定是可见的，就必须使用public；
* static：证明此方法是由类名称调用的；
* void：主方法是一切执行的起点；
* main：Java规定的一个方法名称，不能修改；
* String\[\] args：程序运行时传递的参数；

**范例：**得到参数

```java
public class MainClass {
    public static void main(String[] args) {
        for (int i = 0; i < args.length; i ++) {
            System.out.println(args[i]);
        }
    }
}
```

所有输入的参数必须使用空格分割，例如：“java MainClass 参数 参数 参数”。

如果想要在参数中输入空格，则可以使用“"”描述：“java MainClass "参数 参数"”。

### static的实际应用

在之前得到的结论：

* 不管有多少个对象，都使用同一个static属性；
* 使用static方法可以避免实例化对象调用方法的限制。

**功能一：**实现类实例化对象个数的统计

希望每当实例化一个类对象的时候都可以打印一个信息：产生的第x个实例化对象。

因为只要是新的实例化对象产生了，那么就一定会去调用类中的构造方法，所以可以在构造方法里面增加一个统计数据的操作，每当新对象产生之后统计的内容就自增一个。

```java
class Book {
    private static int num = 0;
    public Book() {
        num ++;
        System.out.println("这是第" + num + "产生的对象！");
    }
}
public class MainClass {
    public static void main(String[] args) {
        new Book();
        new Book();
        new Book();
        new Book();
        new Book();
        new Book();
    }
}
```

**功能二：**实现属性的自动设置

例如，现在某一个类有一个无参构造方法，一个有参构造方法，有参构造方法主要的目的是传递一个title属性，但是希望不管调用的是无参的还是有参的构造，都可以为title设置内容。

```java
class Book {
    private String title;
    private static int num = 0;
    public Book() {
        this("NOTITLE - " + num ++);
    }
    public Book(String title) {
        this.title = title;
    }
    public String getTitle() {
        return this.title;
    }
}
public class MainClass {
    public static void main(String[] args) {
        System.out.println(new Book("Java").getTitle());
        System.out.println(new Book().getTitle());
        System.out.println(new Book().getTitle());
    }
}
```

此时就算是没有设置title属性的内容，那么最终的属性也不会是null。

#### 总结

1、开发之中首选的属性一定不是static属性，首选的方法一定不是static方法；

2、static属性和方法可以在没有实例化对象的时候直接由类名称进行调用；

3、static属性保存在全局数据区

> 内存区一共有四个：栈内存、堆内存、全局数据区、全局代码区。



