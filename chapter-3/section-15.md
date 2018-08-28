## 文件操作

---

使用File类来进行文件的操作。

如果要进行所有的文件以及文件内容的开发操作，应该使用java.io包完成，而在java.io包里面一共有五个核心类和一个核心接口：

* 五个核心类：File、InputStream、OutputStream、Reader、Writer；
* 一个核心接口：Serializable。

在整个java.io包里面，File类是唯一一个与文件本身操作有关的类，但是不涉及到文件的具体内容。文件本身指的是文件的创建、删除等操作。

如果想要使用File类，那么首先就需要通过它提供的构造方法定义一个要操作文件的路径：

* 设置完整路径：public File(String pathname)，大部分情况下使用此类操作；
* 设置父路径与子文件路径：public File(String parent, String child)，在Android上使用比较多。

**范例：**操作文件

* 创建文件：public boolean createNewFile() throws IOException
  * 如果目录不能访问；
  * 文件名称重名或错误；
* 删除文件：public boolean delete()；
* 判断文件是否存在：public boolean exists()；

```java
package com.alpha;
import java.io.File;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:/test.txt"); //设置文件的路径
		if (file.exists()) {
			file.delete();
		} else {
			System.out.println(file.createNewFile());
		}
	}
}
```

以上的程序已经完成了具体的文件创建与删除的操作，但是此时的程序会存在有两个问题：

* 在Windows系统里面支持的是“\”路径分割符，Linux下使用的是“/”路径分隔符；
  * 在File类里面提供有一个常量：public static final String separator；

```java
	File file = new File("d:" + File.separator + "test.txt"); //设置文件的路径
```

* 在进行java.io操作的过程之中，会出现有延迟的情况，因为现在的问题是Java程序是通过JVM间接的调用操作系统的文件处理函数进行的文件处理操作，所以中间会出现延迟情况。

以上已经实现了文件的创建操作，但是这个时候是直接创建在了根目录下，下面来创建包含有子目录的文件。

```java
	File file = new File("d:" + File.separator + "demo" + File.separator + "test.txt"); //设置文件的路径
```

那么现在由于“demo”目录不存在，所以系统会以为此时的路径是不能够使用的，那么就会出现错误。所以现在必须要想办法判断要创建的父路径是否存在。

* 找到父路径：public File getParentFile()；
* 创建目录：public boolean mkdirs()；

```java
package com.alpha;
import java.io.File;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator + "demo" + File.separator + "hello" + File.separator + "mori" + File.separator + "test.txt"); //设置文件的路径
		if (!file.getParentFile().exists()) {
			file.getParentFile().mkdirs(); // 创建父路径
		}
		if (file.exists()) {
			file.delete();
		} else {
			System.out.println(file.createNewFile());
		}
	}
}
```

在File类里面还提供有一系列的取得文件信息内容的操作功能：

* 取得文件的大小：public long length()，按照字节返回；
* 判断是否是文件：public boolean isFile()；
* 判断是否是目录：public boolean isDirectory()；
* 最近一次修改日期：public long lastModified()；

```java
package com.alpha;
import java.io.File;
import java.math.BigDecimal;
import java.text.SimpleDateFormat;
import java.util.Date;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator + "city.jpg"); //设置文件的路径
		if (file.exists()) {
			System.out.println("是否是文件：" + file.isFile());
			System.out.println("是否是目录：" + file.isDirectory()); 
			System.out.println("文件大小：" + new BigDecimal((double)file.length() / 1024 / 1024).divide(new BigDecimal(1), 2, BigDecimal.ROUND_HALF_UP) + " MB");
			System.out.println("上次修改时间：" + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date(file.lastModified()))); 
		}
	}
}
```

整个取得的过程里面取得的都是文件的相关信息，但是不包含文件内容。

以上的所有操作都是围绕着文件进行的，但是在整个磁盘上除了文件之外，还会包含有使用的目录，那么对于目录而言，最为常用的功能应该就是列出目录组成，在File类里面定义有如下的两个列出目录的方法：

* 列出目录下的信息：public String[] list()；
* 列出所有的信息以File类对象包装：public File[] listFiles()。

**范例：**列出信息

```java
package com.alpha;
import java.io.File;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator); //设置文件的路径
		if (file.isDirectory()) {
			String[] result = file.list();
			for (String filename : result) {
				System.out.println(filename);
			}
		}
	}
}
```

**范例：**列出全部的File类对象

```java
package com.alpha;
import java.io.File;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator); //设置文件的路径
		if (file.isDirectory()) {
			File[] result = file.listFiles();
			for (File filename : result) {
				System.out.println(filename);
			}
		}
	}
}
```

```java
package com.alpha;
import java.io.File;
import java.math.BigDecimal;
import java.text.SimpleDateFormat;
import java.util.Date;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator); //设置文件的路径
		if (file.isDirectory()) {
			File[] result = file.listFiles();
			for (File filename : result) {
				System.out.println(filename + "\t\t" 
						+ new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date(file.lastModified())) + "\t\t" 
						+ (filename.isDirectory() ? "文件夹" : "文件") + "\t\t" 
						+ (filename.isFile() ? (new BigDecimal((double)file.length() / 1024 / 1024).divide(new BigDecimal(1), 2, BigDecimal.ROUND_HALF_UP) + " MB") : ""));
			}
		}
	}
}
```

通过一系列验证可以发现，取得文件对象列表会更加的方便，因为可以继续取出跟多的内容。

**思考题：**列出指定目录下的所有子路径

* 原则：如果现在给定的路径依然是一个目录，则应该向里面继续列出所有的组成，应该使用递归的方式完成。

```java
package com.alpha;
import java.io.File;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator);
		print(file);
	}
	public static void print(File file) {
		if (file.isDirectory()) {
			File[] result = file.listFiles();
			if (result != null) {
				for (File filename : result) {
					print(filename);
				}
			}
		}
		System.out.println(file);
	} 
}
```

如果现在将以上的输出变成删除了呢？这就成为了一个恶性程序。

#### 总结

1、File类本身只是操作文件的，不涉及到内容；

2、File类中的重要方法：

* 设置完整路径：public File(String pathname)；
* 删除文件：public boolean delete()；
* 判断文件是否存在：public boolean exists()；
* 找到父路径：public File getParentFile()；
* 创建目录：public boolean mkdirs()；

3、在使用File类操作的时候路径的分隔符使用：File.separator；

