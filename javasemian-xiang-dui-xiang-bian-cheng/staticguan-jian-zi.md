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

![](/assets/import 2.10-1.png)

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

![](/assets/import 2.10-2.png)

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

