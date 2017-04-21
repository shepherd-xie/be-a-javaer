## 深入分析类与对象

---

### 封装性

在研究封装之前，首先观察如下的一段代码：

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book();
        book.title = "Java基础入门";
        book.price = -89.9;
        book.getInfo();
    }
}
```

以上的代码之中没有任何的语法错误，但是却存在有业务逻辑上的错误，因为没有任何一本书的价钱是负数。而造成此类问题的核心的关键在于，对象可以在一个类的外部直接访问属性。

那么现在首先需要解决的问题就是将Book类中的属性设置为对外不可见（只能够针对于本类访问），所以可以使用private关键字来定义属性。

**范例：**修改之前的程序

```java
class Book { //定义一个新的类
    private String title; //书的名字
    private double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book();
        book.title = "Java基础入门";    //The field Book.title is not visible
        book.price = -89.9;            //The field Book.price is not visible
        book.getInfo();
    }
}
```

在访问属性的时候发现，外部的对象无法再直接调用类中的属性了，所以现在等于是属性对外部而言就是不可见的。

但是如果想要让程序可以正常使用，那么必须要想办法让外部的程序可以操作类的属性才可以。所以在开发之中，针对于属性有这样的一种定义：**所有在类中定义的属性都要求使用private声明，如果属性需要被外部所使用，那么需要按照要求定义相应的setter、getter方法，以String title为例：**

* **setter方法主要是设置内容，public void setTitle\(String t\);，有参；**
* **getter方法主要是取得内容，public String getTitle\(\);，无参；**

**范例：**为Book类中的封装属性设置setter、getter操作

```java
class Book { //定义一个新的类
    private String title; //书的名字
    private double price; //书的价格
    public void setTilie(String t) {
        title = t;
    }
    public void setPrice(double p) {
        price = p;
    }
    public String getTitle() {
        return title;
    }
    public double getPrice() {
        return price;
    }
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book();
        book.setTilie("Java基础入门");
        book.setPrice(-89.9);
        book.getInfo();
    }
}
```

如果真的需要加入检查的代码，那么应该在setter之中增加，因为getter只是简单的返回数据。

**范例：**增加验证

```java
public void setPrice(double p) {
    if (p >= 0) {
        price = p;
    }
}
```

对于数据的验证部分，在标准开发之中应该是由其他的辅助代码完成的，而在实际开发之中，setter往往是简单的设置数据，getter往往是简单的取得数据。

#### 总结

1、封装性就是保证类内部的定义被外部不可见，但是本次讲解的封装，只是所有面向对象中封装的最小概念；

2、所有属性都必须使用private封装，封装后的属性如果要被外部访问，要定义setter、getter方法。

### 构造方法与匿名对象

之前我们定义了对象的产生格式：

> ①类名称 ②对象名称 = ③new ④类名称\(\);

* ①类名称：规定了对象的类型，即：对象可以使用那些属性与那些方法，都是由类定义的；
* ②对象名称：如果想要适用对象，需要有一个名字，这是一个唯一的标记；
* ③new：开辟新的堆内存空间，如果没有此语句，对象无法实例化；
* ④类名称\(\)：调用了一个和类名称一样的方法，这就是构造方法。

通过以上的分析可以发现，所有的构造方法实际上一直在被我们调用。但是我们从来没有去定义一个构造方法，之所以能够使用是因为在Java类之中，为了保证程序可以正常的执行，及使用户没有定义任何的构造方法，也会在程序编译之后自动的为类里面添加一个没有参数，方法名与类名称相同，没有返回值的构造方法。

**构造方法的定义原则：方法名称与类名称相同，没有返回值声明。**

```java
class Book {
    public Book() {};    //无参的，无返回值的构造方法
}
```

如果在Book类里面没有写上以上的构造方法，Java也会自动生成一个。

以上只是知道了构造方法一直都存在，但是并额米有发现构造方法有什么样的作用。

```java
class Book {
    public Book() {    //无参的，无返回值的构造方法
        System.out.println("public Book()执行了");
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book book = null;    //声明对象
        book = new Book();    //实例化对象
    }
}
```

通过以上的代码可以发现，所有的构造方法都在对象使用关键字new实例化的时候默认调用。

**构造方法与普通方法的最大区别：**

* 构造方法是在实例化新对象（new）的时候只调用一次。
* 普通方法是在实例化对象产生之后可以随意调用多次。

构造方法是在对象实例化的时候使用的，那么这样做有什么用？

```java
class Book { //定义一个新的类
    private String title; //书的名字
    private double price; //书的价格
    public void setTilie(String t) {
        title = t;
    }
    public void setPrice(double p) {
        if (p >= 0) {
            price = p;
        }
    }
    public String getTitle() {
        return title;
    }
    public double getPrice() {
        return price;
    }
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book();    //使用默认生成的构造方法
        book.setTilie("Java基础入门");
        book.setPrice(89.9);
        book.getInfo();
    }
}
```

本程序是现实例化了Book类对象，而后利用Book类的实例化对象去调用类中定义的setter方法，如果要想设置全部属性的内容，那么一定要调用多次setter方法。

**范例：**定义构造方法

```java
class Book { //定义一个新的类
    private String title; //书的名字
    private double price; //书的价格
    //已经明确定义了一个构造，默认的构造将不再自动生成
    public Book(String t, double p) {
        title = t;
        setPrice(p);    //调用本类中定义的setter方法
    }
    public void setTilie(String t) {
        title = t;
    }
    public void setPrice(double p) {
        if (p >= 0) {
            price = p;
        }
    }
    public String getTitle() {
        return title;
    }
    public double getPrice() {
        return price;
    }
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        //在实例化对象的同时将所需要的类属性传递到对象的构造方法里
        Book book = new Book("Java基础入门", 89.9);
        book.getInfo();
    }
}
```

在实际的工作之中，构造方法的核心作用：在类对象实例化的时候设置属性的初始化内容，构造方法是为属性初始化准备的。

如果一个类之中已经明确的定义了一个构造方法的话，那么不会再自动生成默认的构造方法，即：一个类之中至少保留有一个构造方法。

另外，既然构造方法也属于方法，那么可以针对于构造方法进行重载，但是构造方法由于其定义的特殊性，所以在构造方法重载时，要求只注意参数的类型及个数即可。

**范例：**构造方法重载

```java
class Book { //定义一个新的类
    private String title; //书的名字
    private double price; //书的价格
    public Book() {
        System.out.println("无参构造");
    }
    public Book(String t) {
        System.out.println("有一个参数的构造");
    }
    public Book(String t, double p) {
        System.out.println("有两个参数的构造");
    }
    public void setTilie(String t) {
        title = t;
    }
    public void setPrice(double p) {
        if (p >= 0) {
            price = p;
        }
    }
    public String getTitle() {
        return title;
    }
    public double getPrice() {
        return price;
    }
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book ba = new Book("Java基础入门", 98.8);
    }
}
```

在进行构造方法重载的时候有一点代码编写要求：按照参数的个数进行升序或者降序排列。

> 遗留问题：在定义一个类的时候可以为属性直接设置默认值，但是这个默认值只有在构造执行完后才会设置，否则不会设置，而构造方法是整个对象构造的最后一步，即：是留给用户处理的步骤。
>
> 在对象的实例化过程中，一定会经历类的加载、内存的分配、默认值的设置、构造方法。

既然有了构造方法的概念，实际上就可以依照此方法使用匿名对象。之前定义的都属于有名对象，所有的对象都给了一个名字，但是这个名字真正使用的时候调用的肯定是堆内存空间，即：真实的对象信息都保存在了堆内存空间之中，那么如果没有栈指向的对象就成为匿名对象。

```java
class Book { //定义一个新的类
    private String title; //书的名字
    private double price; //书的价格
    public Book() {
        System.out.println("无参构造");
    }
    public Book(String t) {
        System.out.println("有一个参数的构造");
    }
    public Book(String t, double p) {
        System.out.println("有两个参数的构造");
    }
    public void setTilie(String t) {
        title = t;
    }
    public void setPrice(double p) {
        if (p >= 0) {
            price = p;
        }
    }
    public String getTitle() {
        return title;
    }
    public double getPrice() {
        return price;
    }
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        new Book("Java基础入门", 98.8).getInfo();
    }
}
```

但是匿名对象由于没有其他对象对其进行引用，所以只能使用一次，一次之后该对象空间就将成为垃圾，等待回收。

**疑问？**什么时候使用有名对象，什么时候使用匿名对象？

对于定义对象具体应该定义为各种类型是没有一个具体的规定的，大致上来说，在以后开发过程之中，凡是需要多次调用的都设置为有名对象，凡是只调用一次的，都设置为匿名对象。

#### 总结

1、构造方法的定义要求：方法名称与类名称相同，无返回值声明；

2、构造方法是在类对象使用关键字new实例化的时候被默认调用的，不管代码如何改变，只要有了关键字new就一定需要构造方法；

3、一个类之中至少会保留有一个构造方法，如果没有明确的定义构造方法，那么会自动的生成一个无参的什么都不做的构造方法；

4、构造方法的核心功能是在类对象实例化的时候为类中的属性初始化；

5、构造方法重载时只要求考虑参数的类型及个数即可；

6、匿名对象只能够使用一次。

### 简单Java类

现在要求开发一个雇员的类，里面包含有雇员编号、姓名、职位、基本工资、佣金。

这种功能的类在开发之中称为简单Java类，因为致谢类里面不会包含有过于复杂的程序逻辑。

对于简单Java类而言，现在可以给出它的第一种开发要求：

* 类名称必须存在有意义，例如：Book、Emp；
* 类之中所有属性必须private封装，封装后的属性必须提供有setter、getter；
* 类之中可以提供有任意多个构造方法，但是必须保留有一个无参构造方法；
* 类之中不允许出现任何的输出语句，所有信息输出必须交给被调用处输出；
* 类之中需要提供有一个取得对象完整信息的方法，暂定为：getInfo\(\)，而且返回String型数据；

范例：开发程序类

