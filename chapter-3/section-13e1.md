## 转换流

---

实现字节流与字符流操作的转换。

### 转换流（了解）

字符虽然需要缓冲区进行处理，但是有一个问题，字符输出流有一个特点：可以直接输出一个字符串数据，所以有些时候就不得不进行字节流与字符流的操作转换。

在java.io包里面提供有两个类：InputStreamReader、OutputStreamWriter。这两个类的定义以及构造方法如下：

| 名称 | 定义结构 | 构造方法 |
| :---: | :---: | :---: |
| InputStreamReader | public class InputStreamReader extends Reader | public InputStreamReader(InputStream in) |
| OutputStreamWriter | public class OutputStreamWriter extends Writer | public OutputStreamWriter(OutputStream out) |

**范例：**实现转换

```java
package com.alpha;
import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.io.Writer;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator + "mori" + File.separator + "test.txt");
		if (!file.getParentFile().exists()) {
			file.getParentFile().mkdirs();
		}
		OutputStream output = new FileOutputStream(file); // 字节流
		// 将OutputStream类对象传递给OutputStreamWriter类的构造方法，而后向上转型为Writer
		Writer out = new OutputStreamWriter(output);
		out.write("Hello World!"); // Writer类的方法
		out.flush();
		out.close();
	}
}
```

只有一种情况才会用到这样的操作：方便处理中文数据。

**观察：**分析类的继承结构

**FileInputStream类的继承结构：**

* java.lang.Object
  * java.io.InputStream
    * java.io.FileInputStream

**FileOutputStream类的继承结构：**

* java.lang.Object
  * java.io.OutputStream
    * java.io.FileOutputStream

FileInputStream与FileOutputStream都是InputStream与OutputStream的直接子类，下面继续观察以下FileWriter与FileReader类的继承结构。

**FileReader类的继承结构：**

* java.lang.Object
  * java.io.Reader
    * java.io.InputStreamReader
      * java.io.FileReader

**FileWriter类的继承结构：**

* java.lang.Object
  * java.io.Writer
    * java.io.OutputStreamWriter
      * java.io.FileWriter

文件保存在磁盘上，磁盘能够保存的文件形式都是以字节的方式保存，而在使用字符流读取的时候实际上也是对于字节数据进行读取，只不过这个转换的过程被操作系统给隐藏了，在缓冲区里面进行了数据的操作。

#### 总结

1、如果要进行转换唯一可能出现的形式就是处理中文；

2、两个转换类都是字符流的子类，属于字符字符流与字节流沟通的桥梁。
