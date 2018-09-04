### 对象序列化

* 对象序列化的意义以及实现；
* 了解对象输入、输出流的使用；
* 理解transient关键字。

**对象序列化**

所谓的对象序列化指的就是将保存在内存中的对象数据转换为二进制数据流进行传输的操作。但是并不是所有类的对象都可以进行序列化，想要能够序列化的类必须要实现java.io.Serializable接口。但是这个接口里面没有任何属性和方法存在，因为它是一个标识接口，表示一种能力。

**范例：** 定义一个可以被序列化对象的类
```java
import java.io.Serializable;
@SuppressWarnings("serial")
class Book implements Serializable { // 此类对象可以被序列化
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]";
	}
}
```

**实现序列化与反序列化**

由于现在只有单机程序，所以下面将对象序列化到文件里面，随后再通过文件反序列化到程序之中。如果要想实现这样的操作，那么需要两个类的支持：

* 序列化类：java.io.ObjectOutputStream，将对象变为指定格式的二进制数据；
* 反序列化类：java.io.ObjectInputStream，可以将序列化对象转换回对象。

**范例：** 实现序列化对象操作 —— ObjectOutputStream

* 构造方法：public ObjectOutputStream(OutputStream out) throws IOException
* 输出对象：public final void writeObject(Object obj) throws IOException

```java
	public static void ser() throws Exception {
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(new File("d:" + File.separator + "book.txt")));
		oos.writeObject(new Book("java", 12.5));
		oos.close();
	}
```

**范例：** 实现反序列化操作 —— ObjectInputStream

* 构造方法：public ObjectInputStream(InputStream in) throws IOException
* 读取方法：public final Object readObject() throws IOException, ClassNotFoundException

```java
	public static void dser() throws Exception {
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream(new File("d:" + File.separator + "book.txt")));
		Object obj = ois.readObject();
		Book book = (Book) obj;
		System.out.println(book);
	}
```

在实际的开发之中，会由容器帮助用户自动完成以上的操作。

**transient关键字**

以上虽然实现了对象序列化，但是会发现序列化操作是将整个对象的所有属性内容进行了保存，那么如果说现在某些属性的内容不需要被保存，就可以通过transient关键字来定义。

```java
	private transient String title;
```

此时title属性将无法被序列化。但是大部分情况下不需要使用此关键字。

#### 总结

对象序列化本身就是一个非常简单的概念，但是由于其在开发之中的广泛应用很广泛，所以现在对于编写代码中就必须要清楚Serializable接口的作用，需要注意的是：不是所有的类都需要被序列化，只有需要传输的类才需要被序列化。
