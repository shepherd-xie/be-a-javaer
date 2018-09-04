## 对象比较

---

在比较两个数字是否相等的时候，可以使用“==”来操作，比较两个字符串是否相等可以使用“equals\(\)”来操作，如果说现在有一个自定义的类，该如何来判断这个类的两个对象是否相等。对象=数据集合，实际上判断对象是否相等就是判断对象内的属性是否相等。

**范例：** 基础的比较方式

```java
class Book {
    private String title;
    private double price;
    public Book(String title, double price) {
        this.title = title;
        this.price = price;
    }
    public String getTitle() {
        return this.title;
    }
    public double getPrice() {
        return this.price;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book b1 = new Book("Java基础入门", 79.8); 
        Book b2 = new Book("Java基础入门", 79.8);
        if (b1.getTitle().equals(b2.getTitle())
            && b1.getPrice() == b2.getPrice()) {
            System.out.println("是同一个对象");
        } else {
            System.out.println("不是同一个对象");
        }
    }
}
```

这种形式是一个最基础的对象比较功能的实现，按照以上要求进行类属性的比较。

但是以上的代码存在着诸多问题：

* 主方法是一个客户端，要求逻辑越简单越好，最好隐藏所有细节逻辑。

如果想要进行比较，那么具体的细节应由每个对象自己完成，对象类中一定要自己提供对象比较的方法，这个方法暂定compare\(\)。

**范例：** 对象比较实现

```java
class Book {
    private String title;
    private double price;
    public Book(String title, double price) {
        this.title = title;
        this.price = price;
    }
    public String getTitle() {
        return this.title;
    }
    public double getPrice() {
        return this.price;
    }
    public boolean compare(Book book) {
        if (book == null) {
            return false;
        }
        if (this == book) {
            return true;
        }
        if (this.getTitle().equals(book.getTitle())
                && this.getPrice() == book.getPrice()) {
            return true;
        } else {
            return false;
        }
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book b1 = new Book("Java基础入门", 79.8); 
        Book b2 = new Book("Java基础入门", 79.8);
        if (b1.compare(b2)) {
            System.out.println("是同一个对象");
        } else {
            System.out.println("不是同一个对象");
        }
    }
}
```



