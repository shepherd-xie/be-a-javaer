## IO高级应用

---

### 缓冲输入流

缓冲输入流是在开发之中，也会经常被使用到的工具类，其目的是解决数据的乱码问题。

现在最直观的问题就是System.in所带来的问题。

如果要进行中文数据的处理那么就会使用到字符流，如果想要完整的处理数据，那么一定需要一个缓冲区。对于缓冲区的操作有两种流：

* 字符缓冲流：BufferedReader、BufferWriter；
* 字节换成流：BufferedInputStream、BufferedOutputStream；

在给出的缓冲区数据输入流上有两个，其中最为重要的就是BufferedReader，因为在BufferedReader类里面提供有一个重要的读取方法：public String readLine() throws IOException，读取一行数据，以分隔符（"\n"）为界。

下面来观察BufferedReader类的继承结构以及构造方法；

* java.lang.Object
  * java.io.Reader
    * java.io.BufferedReader

Constructor：public BufferedReader(Reader in)

但是此时如果要想使用BufferedReader类来处理System.in就比较麻烦了，因为System.in是InputStream类。在之前学习过一个类：InputStreamReader。

**范例：**键盘输入数据的标准格式

```java
package com.alpha;
import java.io.BufferedReader;
import java.io.InputStreamReader;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// System.in是InputStream类对象
		// BufferedReader的构造方法接收Reader类对象
		// 利用InputStreamReader将字符流变为字节流
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		System.out.print("请输入数据：");
		String str = br.readLine(); // 以回车作为换行
		System.out.println("输入的内容：" + str);
	}
}
```

此时输入的数据没有长度限制，并且返回的还是String型数据，那么这样就可以实现键盘的输入，但是这种操作一般意义不大。

使用BufferedReader是因为他可以实现字符串数据的接收，所以现在可以实现基于正则的判断。

**范例：**判断输入内容

```java、
package com.alpha;
import java.io.BufferedReader;
import java.io.InputStreamReader;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		boolean flag = true;
		while (flag) {
			System.out.print("请输入年龄：");
			String str = br.readLine();
			if (str.matches("\\d{1,3}")) { // 输入的数据由数字组成
				System.out.println("年龄是：" + Integer.parseInt(str));
				flag = false;
			} else {
				System.out.println("年龄输入错误");
			}
		}
	}
}
```

正式因为此处可以使用正则进行操作验证，所以在开发的过程中，最方便的是能够接收String类型的数据。

除了可以接受输入信息之外，也可以利用缓冲区进行文件的读取。

```java
package com.alpha;
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator + "my.txt");
		BufferedReader br = new BufferedReader(new FileReader(file));
		String str = null;
		while ((str = br.readLine()) != null) {
			System.out.println(str);
		}
		br.close();
	}
}
```

与直接使用InputStream（Reader）类相比，使用BufferedReader在进行文件信息读取的时候会更加的方便。

#### 总结

读取数据不再直接使用InoutStream，就好比输出不再使用OutputStream一样。

### 扫描流：Scanner

1、Scanner类的主要她点以及操作形式；

2、利用Scanner解决输入流的操作。

如果要改进输出功能不足体用有打印流，随后又利用了BufferedReader解决了大文本数据的读取操作，但是BufferedReader类有两个问题：

* 它读取数据的时候只能够按照字符串返回：public String readLine() throws IOException；
* 所有的分隔符都是固定的。

在JDK1.5后提供有体格java.util.Scanner的类，这个类专门负责解决所有输入流的操作问题。

构造方法：public Scanner(InputStream source)，接收有一个InputStream类对象，表示的是由外部设置输入的位置。

在Scanner类里面定义了一下的两组方法：

* 判断是否有指定数据：public boolean hasNext()；
* 取出数据：public String next()。

**范例：**以键盘输入数据为例

```java
package com.alpha;
import java.util.Scanner;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		Scanner scan = new Scanner(System.in);
		System.out.print("请输入内容：");
		if (scan.hasNext()) { // 现在有输入数据
			System.out.println("输入内容：" + scan.next());
		}
		scan.close();
	}
}
```

Scanner与BufferedReader类的操作相比，Scanner更加的容易，并且操作更为直观。

但是需要提醒的是，如果输入的是字符串，是否存在有hasNext()方法意义不大。但是如果是其他的数据类型，hasNext()就有意义了，为了保持操作的统一性，不管输入的是什么类型都要有hasNext()方法。

**范例：**输入一个数字 —— double

```java
package com.alpha;
import java.util.Scanner;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		Scanner scan = new Scanner(System.in);
		System.out.print("请输入成绩：");
		if (scan.hasNextDouble()) {
			double score = scan.nextDouble();
			System.out.println("输入内容：" + score);
		} else {
			System.out.println("输入的不是数字");
		}
		scan.close();
	}
}
```

除了以上支持的各种可行外，也可以在Scanner输入数据的时候设置正则验证。

**范例：**正则验证

```java
package com.alpha;
import java.util.Scanner;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		Scanner scan = new Scanner(System.in);
		System.out.print("请输入生日：");
		if (scan.hasNext("\\d{4}-\\d{2}-\\d{2}")) {
			String bir = scan.next("\\d{4}-\\d{2}-\\d{2}");
			System.out.println("输入内容：" + bir);
		} else {
			System.out.println("输入的格式错误");
		}
		scan.close();
	}
}
```

在Scanner类的构造里面由于接受的类型是InputStream，所以此时依然可以设置一个文件的数据流，但是在进行文件读取的时候需要考虑到分隔符问题：public Scanner useDelimiter(String pattern)。

```java
package com.alpha;
import java.io.File;
import java.io.FileInputStream;
import java.util.Scanner;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		Scanner scan = new Scanner(new FileInputStream(new File("d:" + File.separator + "my.txt")));
		scan.useDelimiter("\r\n"); // 设置换行符
		while (scan.hasNext()) {
			System.out.println(scan.next());
		}
		scan.close();
	}
}
```

> ### 关于\r、\n的一些冷知识
> 
> * \r = CR (Carriage Return) // moves the cursor to the beginning of the line without advancing to the next line. -Used as a new line character in Mac OS before X
> * \n = LF (Line Feed) // moves the cursor down to the next line without returning to the beginning of the line. -Used as a new line character in Unix/Mac OS X
> * \r\n = CR + LF // a combination of \r and \n. -Used as a new line character in Windows
> 
> 在计算机还没有出现之前，有一种叫做电传打字机（Teletype Model 33）的玩意，每秒钟可以打10个字符。但是它有一个问题，就是打完一行换行的时候，要用去0.2秒，正好可以打两个字符。要是在这0.2秒里面，又有新的字符传过来，那么这个字符将丢失。
> 
> 于是，研制人员想了个办法解决这个问题，就是在每行后面加两个表示结束的字符。一个叫做“回车”，告诉打字机把打印头定位在左边界；另一个叫做“换行”，告诉打字机把纸向下移一行。
> 
> 这就是“换行”和“回车”的来历，从它们的英语名字上也可以看出一二。
> 
> 后来，计算机发明了，这两个概念也就被般到了计算机上。那时，存储器很贵，一些科学家认为在每行结尾加两个字符太浪费了，加一个就可以。于是，就出现了分歧。
> 
> * Unix系统里，每行结尾只有“<换行>”，即“\n”；
> * Windows系统里面，每行结尾是“<回车><换行>”，即“\r\n”；
> * Mac系统里，每行结尾是“<回车>”，即“\r”。
> 
> 一个直接后果是，Unix/Mac系统下的文件在Windows里打开的话，所有文字会变成一行；而Windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号。

现在使用Scanner读取数据的时候综合来讲的确要比BufferedReader简单一些，所以在以后的开发之中，程序输出数据使用打印流，输入数据使用扫描流。

#### 总结

InputStream类的功能不足被Scanner替代了。

Reader类的功能不足被BufferedReader替代了。

OutputStream类的功能不足被PrintStream替代了。

Writer类的功能不足被PrintWriter替代了。

### 对象序列化

1、对象序列化的意义以及实现；

2、了解对象输入、输出流的使用；

3、理解transient关键字。

**对象序列化**

所谓的对象序列化指的就是将保存在内存中的对象数据转换为二进制数据流进行传输的操作。但是并不是所有类的对象都可以进行序列化，想要能够序列化的类必须要实现java.io.Serializable接口。但是这个接口里面没有任何属性和方法存在，因为它是一个标识接口，表示一种能力。

**范例：**定义一个可以被序列化对象的类

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

**范例：**实现序列化对象操作 —— ObjectOutputStream

* 构造方法：public ObjectOutputStream(OutputStream out) throws IOException
* 输出对象：public final void writeObject(Object obj) throws IOException

```java
	public static void ser() throws Exception {
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(new File("d:" + File.separator + "book.txt")));
		oos.writeObject(new Book("java", 12.5));
		oos.close();
	}
```

**范例：**实现反序列化操作 —— ObjectInputStream

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
