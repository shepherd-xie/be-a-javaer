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

### 构造方法与匿名对象



