## 文件拷贝

---

模拟DOS系统的文件拷贝程序，实现字节流的综合使用。

### 文件拷贝

在DOS系统之中文件拷贝的命令：copy 源文件路径 目标文件路径。

如果想要实现文件的拷贝操作，那么现在有两种做法：

* 方法一：将所有的文件内容一次性读取到程序之中，而后一次性输出；
  * 需要开辟一个与文件大小一样的数组，但是如果文件过大呢？程序就会崩溃。
* 方法二：采用边读边输出的方式，那么现在就不会占用过大的内存空间；

由于现在有可能要拷贝二进制数据，那么一定使用字节流会比较合适一些。

**范例：**代码的基本实现

```java
package com.alpha;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
public class CopyDemo {
	public static void main(String[] args) throws Exception {
		long start = System.currentTimeMillis();
		if (args.length != 2) { // 初始化参数不足2位
			System.out.println("命令执行错误！");
			System.exit(1); // 程序退出执行
		}
		// 如果输入的参数正确，那么应该进行文件的验证
		File inFile = new File(args[0]); // 第一个为源文件路径
		if (!inFile.exists()) { // 源文件不存在
			System.out.println("源文件不存在，请确认执行路径。");
			System.exit(1); // 程序退出
		}
		// 如果此时源文件正确，那么就需要定义输出文件，同时要考虑到输出文件有目录
		File outFile = new File(args[1]);
		if (!outFile.getParentFile().exists()) { // 输出文件路径不存在
			outFile.getParentFile().mkdirs(); // 创建目录
		}
		// 实现文件内容的拷贝
		InputStream input = new FileInputStream(inFile);
		OutputStream output = new FileOutputStream(outFile);
		// 实现文件拷贝
		int temp = 0; // 保存每次读取的内容
		while ((temp = input.read()) != -1) { // 每次读取单个字节
			output.write(temp); // 输出单个字节
		}
		input.close();
		output.close();
		long end = System.currentTimeMillis();
		System.out.println("拷贝所花费的时间：" + (end - start));
	}
}
```

这个时候发现，本程序的确可以实现拷贝，但是拷贝的速度实在太慢。现在的程序每次读取一个字节，那么这个循环所耗费的时间就太长了。如果要想改变以上代码的性能问题，那么就必须采用部分数据读取并保存的方式，回顾一下字节流的两个操作方法：

* InputStream：public int read(byte[] b) throws IOException
  * 将内容读取到字节数组之中，如果没有数据了返回的是-1，否则就是读取长度；
* OutputStream：public void write(byte[] b, int off, int len) throws IOException；
  * 要设置的字节数组实际上就是在read()方法里面使用的数组；
  * 一定是从字节数组的第0个元素开始输出，输出读取的数据长度；

**范例：**改进拷贝操作

```java
package com.alpha;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
public class CopyDemo {
	public static void main(String[] args) throws Exception {
		long start = System.currentTimeMillis();
		if (args.length != 2) { // 初始化参数不足2位
			System.out.println("命令执行错误！");
			System.exit(1); // 程序退出执行
		}
		// 如果输入的参数正确，那么应该进行文件的验证
		File inFile = new File(args[0]); // 第一个为源文件路径
		if (!inFile.exists()) { // 源文件不存在
			System.out.println("源文件不存在，请确认执行路径。");
			System.exit(1); // 程序退出
		}
		// 如果此时源文件正确，那么就需要定义输出文件，同时要考虑到输出文件有目录
		File outFile = new File(args[1]);
		if (!outFile.getParentFile().exists()) { // 输出文件路径不存在
			outFile.getParentFile().mkdirs(); // 创建目录
		}
		// 实现文件内容的拷贝
		InputStream input = new FileInputStream(inFile);
		OutputStream output = new FileOutputStream(outFile);
		// 实现文件拷贝
		int temp = 0; // 保存每次读取的个数
		byte[] data = new byte[1024]; // 每次读取1024个字节
		// 将每次读取进来的数据保存在字节数组里面，并且返回读取的个数
		while ((temp = input.read(data)) != -1) {
			output.write(data, 0, temp); // 输出数组
		}
		input.close();
		output.close();
		long end = System.currentTimeMillis();
		System.out.println("拷贝所花费的时间：" + (end - start));
	}
}
```

#### 总结

本程序属于之前的一个总结程序，能够把本程序掌握清楚，就可以轻松的掌握InputStream、OutputStream两个类的核心操作，并且在以后的开发之中，以上的代码100%会使用到。

```java
```
