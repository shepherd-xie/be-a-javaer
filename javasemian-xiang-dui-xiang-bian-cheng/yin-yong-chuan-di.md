## 引用传递

---

引用传递是整个Java的精髓所在，下面将利用三个程序对引用传递做进一步的分析。

引用传递的核心意义：同一块堆内存空间可以被不同的栈内存所指向，不同栈内存可以对同一堆内存进行内容的修改。

**范例一：**

```java
class Message {
    private int num = 10;
    public Message(int num) {
        this.num = num;
    }
    public void setNum(int num) {
        this.num = num;
    }
    public int getNum() {
        return this.num;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Message msg = new Message(30);
        fun(msg);
        System.out.println(msg.getNum());
    }
    public static void fun(Message temp) {
        temp.setNum(100);
    }
}
```

![](/assets/import 2.7-1.png)**范例二：**

```java
public class MainClass {
    public static void main(String[] args) {
        String msg = "Hello";
        fun(msg);
        System.out.println(msg);
    }
    public static void fun(String temp) {
        temp = "World";
    }
}
```

要注意的只有一点，String类对象的内容一旦声明，对象内容的改变依靠的是引用地址的改变。![](/assets/import 2.7-2.png)**范例三：**

```java
class Message {
    private String info = "nihao";
    public Message(String info) {
        this.info = info;
    }
    public void setInfo(String info) {
        this.info = info;
    }
    public String getInfo() {
        return this.info;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Message msg = new Message("Hello");
        fun(msg);
        System.out.println(msg);
    }
    public static void fun(Message temp) {
        temp.setInfo("World");
    }
}
```

![](/assets/import 2.7-3.png)但是以上的内存描述并不严格，因为在程序里面，info也是一个String类的对象，所以如果想要描述清楚，应该使用如下的内存关系图。

![](/assets/import 1.7-4.png)

虽然String属于类，属于引用类型，但是由于其内容不可改变的特点，很多时候就直接把String当成基本数据类型那样使用。也就是说，每一个String类型只能够保存一个数据。

