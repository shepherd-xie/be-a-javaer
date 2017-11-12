## 字节流与字符流

---

File类虽然可以操作文件，但是并不是操作文件的内容，如果要进行内容的操作只能通过两种途径完成：字节流、字符流。

如果要进行输入、输出操作一般都会按照如下的步骤进行（以文件操作为例）：

* 通过File类定义一个要操作文件的路径；
* 通过字节流或字符流的子类对象为父类对象实例化；
* 进行数据的读（输入）、写（输出）操作；
* 数据流属于资源操作，资源操作必须关闭。

对于java.io包而言它定义了两类流：

* 字节流（JDK1.0）：InputStream、OutputStream；
* 字符流（JDK1.1）：Reader、Writer。

> ### 流是什么？
>
> _引用自 [Java™教程](https://docs.oracle.com/javase/tutorial/essential/io/streams.html)_
>
> I / O流表示输入源或输出目标。 流可以表示许多不同类型的源和目标，包括磁盘文件，设备，其他程序和内存数组。
>
> 流支持许多不同类型的数据，包括简单字节，原始数据类型，本地化字符和对象。 有些流只是传递数据。 其他人以有用的方式操作和转换数据。
>
> 无论内部是如何工作的，所有流都使用相同的简单模型：程序流是一系列数据。
>
> 原文：
>
> > _Quoted from [The Java™ Tutorials](https://docs.oracle.com/javase/tutorial/essential/io/streams.html)_
> > 
> > An I/O Stream represents an input source or an output destination. A stream can represent many different kinds of sources and destinations, including disk files, devices, other programs, and memory arrays.
> > 
> > Streams support many different kinds of data, including simple bytes, primitive data types, localized characters, and objects. Some streams simply pass on data; others manipulate and transform the data in useful ways.
> > 
> > No matter how they work internally, all streams present the same simple model to programs that use them: A stream is a sequence of data.


> 

### 字节输出流：OutputStream（重点）

OutputStream类是一个专门进行字节数据输出的一个类，这个类定义如下：

```java
public abstract class OutputStream
extends Object
implements Closeable, Flushable
```

首先可以发现OutputStream类实现了两个接口：Closeable、Flushable，这两个接口定义如下：

**Closeable接口：JDK1.5之后提供**

```java
public interface Closeable extends AutoCloseable {
	public void close() throws IOException;
}
```

**Flushable接口：JDK1.5之后提供**

```java
public interface Flushable {
	public void flush() throws IOException;
}
```

同时，在JDK1.7的时候Java引入了一个自动关闭机制，所以让Closeable接口继承了AutoCloseable接口，这个父借口的定义如下：

```java
public interface AutoCloseable {
	public void close() throws Exception;
}
```

但是OutputStream类是在JDK1.0的时候就提供的，这个类原本就定义了close()和flush()两个操作方法，所以现在以上的两个接口就几乎可以忽略了。

在OutputStream类里面一共提供有三个输出的方法：

* 输出单个字节：public abstract void write(int b) throws IOException；
* 输出全部字节数组：public void write(byte[] b) throws IOException；
* 输出部分字节数组：public void write(byte[] b, int off, int len) throws IOException;

OutputStream本书属于抽象类，如果想要为抽象类进行对象的实例化操作，那么一定要使用抽象类的子类，本次可以使用FileOutputStream子类。在这个子类里面定义有如下的构造方法：

* 创建或覆盖已有文件：public FileOutputStream(File file) throws FileNotFoundException；
* 文件内容追加：public FileOutputStream(File file, boolean append) throws FileNotFoundException；

**范例：**文件内容的输出

```java
package com.alpha;
import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 1.定义要输出文件的路径
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		// 2.此时目录不存在，所以要先创建目录
		if (!file.getParentFile().exists()) { // 文件目录不存在
			file.getParentFile().mkdirs(); // 创建目录
		}
		// 3.使用OutputStream和其子类进行对象的实例化
		OutputStream output = new FileOutputStream(file);
		// 4.进行文件内容的输出
		String str = "好好学习，天天向上！";
		byte[] data = str.getBytes(); // 将字符串变为字节数组
		output.write(data); // 将内容输出
		// 5.关闭流
		output.close();
	}
}
```

以上是将整个字节输出的内容进行了输出，并且发现，如果此时要输出的文件不存在，那么会自动进行创建，可是对于输出操作在整个OutputStream类里面一共定义有三个方法。

**范例：**采用单个字节的方式输出

```java
	for (int i = 0; i < data.length; i ++) {
		output.write(data[i]); // 将内容输出
	}
```

**范例：**输出部分字节数组内容

```java
	output.write(data, 6, 6);
```

但是每一次都是覆盖不是很好，希望可以实现内容的追加，那么只需要更换FileOutputStream类的构造方法即可。

```java
	// 3.使用OutputStream和其子类进行对象的实例化
	OutputStream output = new FileOutputStream(file, true);
	// 4.进行文件内容的输出
	String str = "好好学习，天天向上！\r\n";
```

只要是程序要输出内容，都可以利用OutputStream类完成。

### 字节输入流：InputStream

如果程序需要进行数据的读取操作，可以利用InputStream类实现功能，此类定义如下：

```java
public abstract class InputStream
extends Object
implements Closeable
```

虽然此类实现了Closeable接口但是与OutputStream类一样，不用去考虑此接口的存在，在InputStream类里面也定义有数据读取的方法：

* 读取单个字节：public abstract int read() throws IOException；
  * 返回值：返回读取的字节内容，如果现在已经没有内容可以读，则返回-1；
* 将读取的数据保存在字节数组里：public int read(byte[] b) throws IOException；
  * 返回值：返回读取的数组长度，如果读取到结尾，返回-1；
* 将读取的数组保存在部分字节数组里：public int read(byte[] b, int off, int len) throws IOException；
  * 返回值：读取的部分数据的长度，如果已经读到结尾了，返回-1。

InputStream是一个抽象类，所以如果要想进行文件读取使用FileInputStream子类，这个子类的构造如下：

* 构造方法：public FileInputStream(File file) throws FileNotFoundException；

**范例：**向数组里面读取数据

```java
package com.alpha;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 1.定义要输入文件的路径
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		// 2.如果文件存在才可以进行读取
		if (file.exists()) { // 文件存在
			// 3.使用InputStream进行读取
			InputStream input = new FileInputStream(file);
			// 4.进行数据读取
			byte[] data = new byte[1024]; // 准备出一个1024的数组
			int len = input.read(data); // 将内容保存到字节数组之中
			// 5.关闭输入流
			input.close();
			System.out.println("【" + new String(data, 0, len) + "】");
		}
	}
}
```

**范例：**单个字节数据读取

* 由于一个文件有很多的字节数据，所以如果要进行读取肯定采用循环的方式完成，由于不确定循环次数，所以应该采用while循环完成，下面为了更好的演示出问题，使用do_while与while两种方式实现。

**实现一（不用）：**使用do_while

```
package com.alpha;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 1.定义要输入文件的路径
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		// 2.如果文件存在才可以进行读取
		if (file.exists()) { // 文件存在
			// 3.使用InputStream进行读取
			InputStream input = new FileInputStream(file);
			// 4.进行数据读取
			byte[] data = new byte[1024]; // 准备出一个1024的数组
			int foot = 0; // 表示字节数组的操作角标
			int temp = 0; // 表示接受每次读取的字节数据
			do {
				temp = input.read(); // 读取一个字节
				if (temp != -1) {
					data[foot ++] = (byte) temp; // 保存读取的字节到数组之中
				}
			} while(temp != -1); // 如果现在读取的temp的字节数据不是-1，表示还有内容
			// 5.关闭输入流
			input.close();
			System.out.println("【" + new String(data, 0, foot) + "】");
		}
	}
}
```

**实现二（重点）：**利用while循环读取

```java
package com.alpha;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 1.定义要输入文件的路径
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		// 2.如果文件存在才可以进行读取
		if (file.exists()) { // 文件存在
			// 3.使用InputStream进行读取
			InputStream input = new FileInputStream(file);
			// 4.进行数据读取
			byte[] data = new byte[1024]; // 准备出一个1024的数组
			int foot = 0; // 表示字节数组的操作角标
			int temp = 0; // 表示接受每次读取的字节数据
			// 第一部分：(temp = input.read())，表示将read()方法读取的字节内容给了temp变量
			// 第二部分：(temp = input.read()) != -1，判断读取出来的temp内容是否是-1  
			while ((temp = input.read()) != -1) {
				data[foot ++] = (byte) temp; // 有内容进行保存
			}
			// 5.关闭输入流
			input.close();
			System.out.println("【" + new String(data, 0, foot) + "】");
		}
	}
}
```

此类读取数据的方式是在以后的开发之中使用最多的情况。

### 字符输出流：Writer

Writer类是在JDK1.1之后增加的，其类的定义如下：

```java
public abstract class Writer
extends Object
implements Appendable, Closeable, Flushable
```

这个类除了实现了Closeable与Flushable接口外，又多实现了一个Appendable，这个接口定义如下：

```java
public interface Appendable {
	public Appendable append(char c) throws IOException;
	public Appendable append(CharSequence csq) throws IOException;
	public Appendable append(CharSequence csq, int start, int end) throws IOException;
}
```

在Appendable接口里面定义了追加的操作，而且追加的数据都是字符或者是字符串。

在Wirter类里面定义有以下的输出方法（部分）：

* 输出全部字符数组：public void write(char[] cbuf) throws IOException；
* 输出字符串：public void write(String str) throws IOException；

Writer是一个抽象类，如果要想为这个类的对象实例化，应该使用FileWriter子类；

**范例：**使用Writer类实现内容输出

```java
package com.alpha;
import java.io.File;
import java.io.FileWriter;
import java.io.Writer;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 1.定义要输出文件的路径
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		if (!file.getParentFile().exists()) {
			file.getParentFile().mkdirs();
		}
		// 2.实例化Writer类的对象
		Writer out = new FileWriter(file);
		// 3.内容输出
		String str = "Hello World！";
		out.write(str); // 输出字符串数据
		// 4.关闭输出流
		out.close();
	}
}
```

可以发现Writer作为字符输出流，可以直接进行字符串的输出，这一点就比OutputStream要强。

### 字符输入流：Reader

Reader是进行字符数据读取的输入流，其本身也是一个抽象类：

```java
public abstract class Reader
extends Object
implements Readable, Closeable
```

在Reader类里面也提供有一系列的read()方法：

* 读取内容到字符数组：public int read(char[] cbuf) throws IOException；
  * 返回值：表示读取的数据长度，如果已经读取到结尾了返回-1；

为Reader类实例化的可以使用FileReader子类完成。

**范例：**使用Reader读取数据 

```java
package com.alpha;
import java.io.File;
import java.io.FileReader;
import java.io.Reader;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		// 1.定义要输出文件的路径
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		if (file.exists()) {
			// 2.为Reader类对象实例化
			Reader in = new FileReader(file);
			// 3.进行数据读取
			char[] data = new char[1024];
			int len = in.read(data);
			// 4.关闭输入流
			in.close();
			System.out.println(new String(data, 0, len));
		}
	}
}
```

与字节输入流相比结构几乎是一样的，只是数据类型由byte更换为了char而已。

### 字节流与字符流的区别？

现在已经给除了两种数据流，那么开发之中到底该使用哪一种？有什么区别呢？

字节流与字符流最大的区别是：字节流直接与终端进行数据交互，而字符流需要将数据经过缓冲区处理后才可以输出。

在使用OutputStream输出数据的时候即使最后没有关闭输出流，那么内容也可以正常输出，但是返过来如果使用的是字符输出流，如果不关闭，那么就表示在缓冲区之中处理的内容不会被强制性的清空，所以就不会输出数据。如果现在有特殊情况不能够关闭字符输出流，可以使用flush()方法强制清空缓冲区。

**范例：**强制清空字符流缓冲区

```java
package com.alpha;
import java.io.File;
import java.io.FileWriter;
import java.io.Writer;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		if (!file.getParentFile().exists()) {
			file.getParentFile().mkdirs();
		}
		Writer out = new FileWriter(file);
		String str = "Hello World!";
		out.write(str); // 输出字符串数据
		out.flush(); // 强制清空缓冲区
	}
}
```

在开发之中，对于字节数据处理是比较多的，例如：图片、音乐、电影、文字、压缩包。而对于字符流最大的好处是它可以进行中文的有效处理，那么在日后的开发之中，如果要处理中文的时候请优先考虑字符流，如果没有中文问题建议使用字节流。

#### 总结

1、字节流的输入输出操作的代码；

* OutputStream与InputStream类中的write()和read()方法必须掌握清楚；

2、中文处理的时候强烈建议使用字符流。

