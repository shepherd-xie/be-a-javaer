## this关键字

---

this关键字是Java中比较复杂的一个关键字，因为this有一个核心概念：当前对象，而这个当前对象就很难去理解。

在Java里面，利用this关键字可以实现类属性的调用、类方法的调用、表示当前对象。

### 调用属性

下面来观察如下的一段代码。

**范例：**观察程序

```java
class Book {
	private String title;
	private double price;
	public Book(String t, double p) {
		title = t;
		price = p;
	}
	//setter、getter略
	public String getInfo() {
		return "书名：" + title + "，价格：" + price;
	} 
}
public class MainClass {
	public static void main(String[] args) {
		Book book = new Book("Java基础入门", 89.8);
		System.out.println(book.getInfo());
	}
}
```

观察如下的一点：

```java
public Book(String t, double p) {
	title = t;
	price = p;
}
```

此处使用的是一个构造方法，而这个构造方法的主要功能是为title与price初始化使用，但是方法中的参数名称无法

起到“见名知意”的效果。既然构造方法中的参数的目的是为了类中的属性初始化，那么最好的做法是直接将参数设

置为与属性名称保持一致。

```java
public Book(String title, double price) {
	title = title;
	price = price;
}
```

修改为以上的形式参数就比较合理，但是最终发现在构造方法传递的参数内容并没有传递到属性之中。

在Java程序里面它是以“{}”为界限。如果现在属性名称与参数名称出现了重名的情况下，默认情况如果没有加入任

何的限制，值得都是最近的“{}”内的变量名称。所以在这种情况下为了可以明确的找到要访问的变量属于类中的属

性的时候，需要在变量前加上this，这样就可以准确的进行属性的标记。

```java
class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	//setter、getter略
	public String getInfo() {
		return "书名：" + this.title + "，价格：" + this.price;
	} 
}
public class MainClass {
	public static void main(String[] args) {
		Book book = new Book("Java基础入门", 89.8);
		System.out.println(book.getInfo());
	}
}
```

在以后程序开发之中，只要是访问类中的属性前面必须加上“this.”。

