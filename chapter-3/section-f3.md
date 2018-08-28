## 内省

### 内省的基本概念

内省(Introspector) 是Java 语言对 JavaBean 类属性、事件的一种缺省处理方法。

JavaBean（即简单Java类）是一种特殊的类，主要用于传递数据信息，这种类中的方法主要用于访问私有的字段，且方法名符合某种命名规则。如果在两个模块之间传递信息，可以将信息封装进JavaBean中，这种对象称为“值对象”(Value Object)，或“VO”。方法比较少。这些信息储存在类的私有变量中，通过set()、get()获得。

```java
public class Book {
    private String title;
    private double price;
    public void setTitle(String title) {
        this.title = title;
    }
    public String getTitle() {
        return title;
    }
    public void setPrice(double price) {
        this.price = price;
    }
    public double getPrice() {
        return price;
    }
}
```

在类Book中有属性 title, 那我们可以通过 setTitle,getTitle来得到其值或者设置新的值。通过 getTitle/setTitle来访问 title属性，这就是默认的规则。 Java JDK中提供了一套 API 用来访问某个属性的 getter/setter 方法，这就是内省。

### 内省的实现

Java提供了一个类处理内省的操作PropertyDescriptor，具体操作内容如下：
