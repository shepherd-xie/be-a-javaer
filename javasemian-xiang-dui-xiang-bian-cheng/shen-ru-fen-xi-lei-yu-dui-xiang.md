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
		book.title = "Java基础入门";	//The field Book.title is not visible
		book.price = -89.9;			//The field Book.price is not visible
		book.getInfo();
	}
}
```



