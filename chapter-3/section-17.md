## IO辅助概念

* [字符编码](/chapter-3/section-17.md#字符编码)
* [内存流](/chapter-3/section-17.md#内存流)
* [管道流](/chapter-3/section-17.md#管道流)
* [RandomAccessFile](/chapter-3/section-17.md#RandomAccessFile)

### 字符编码

了解常见的字符编码。

了解乱码的产生原因。

计算机中所有的信息组成都是二进制数据，那么所有能够描述出的中文文字都是经过处理后的结果。在计算机的世界里，所有的语言文字都会使用编码来进行描述，例如：最常见的编码是ASCII码。在实际的工作里面最为常见的几种编码如下：

* GBK、GB2312：中文的国标编码，其中GBK包含有简体中文与繁体中文两种，而GB2312只包含简体；
* ISO8859-1：是国际编码，可以描述任何的文字信息；
* UNICODE：是十六进制编码，造成传输的无用数据过多；
* UTF编码（UTF-8）：融合了ISO8859-1和UNICODE编码的特点；

在以后的所有开发里面，使用的都是UTF-8编码。

所谓的乱码最本质的方式就是编码与解码的字符集不统一。如果要想知道现在系统能够使用的编码，那么可以列出所有的环境属性。

```java
package com.alpha;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		System.getProperties().list(System.out);
	}
}
```

`file.encoding=GBK`
`file.separator=\`

发现默认的编码是GBK，那么也就是说默认输出的中文都是GBK编码标准。

**范例：**默认的中文输出

```java
package com.alpha;
import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator + "test.txt");
		OutputStream output = new FileOutputStream(file);
		output.write("你好，世界！".getBytes());
		output.close();
	}
}
```

**范例：**出现乱码

```java
package com.alpha;
import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File file = new File("d:" + File.separator + "test.txt");
		OutputStream output = new FileOutputStream(file);
		output.write("你好，世界！".getBytes("ISO8859-1"));
		output.close();
	}
}
```

所谓的乱码最本质的就是编码集不统一。

#### 总结

1、以后开发的代码使用的都是UTF-8编码；

2、乱码的本质就是编码与解码不统一。

### 内存流

可以使用内存流实现IO操作

在之前使用过了文件操作流实现了针对于文件数据的输入与输出操作，但是如果现在某一种应该用，需要进行IO操作，可是又不想产生文件的时候，就可以利用内存来实现输入与输出的操作。

针对于内存流，java.io包里面提供了两组操作：

* 字节内存流：ByteArrayInputStream、ByteArrayOutputStream
* 字符内存流：CharArrayReader、CharArrayWriter

本次是以字节内存流操作为主。下面重点来看ByteArrayInputStream与ByteArrayOutputStream的继承结构与构造方法。

ByteArrayInputStream

* java.lang.Object
  * java.io.InputSteram
    * java.io.ByteArrayInputStream

Constructor：public ByteArrayInputStream(byte[] buf)，表示将要操作的数据设置到输入流

ByteArrayOutputStream

* java.lang.Object
  * java.io.OutputStream
    * java.io.ByteArrayOutputStream

Constructor：public ByteArrayOutputStream()，从内存输出数据

下面为了更好的说明出问题，特别做一个举例：

* 以文件操作为例：
  * 输出（OutputStream）：程序 -> OutputStream -> 文件；
  * 输入（InputStream）：程序 <- InputStream <- 文件；
* 以内存操作为例：
  * 输出（InputStream）：程序 -> InputStream -> 内存；
  * 输入（OutputStream）：程序 <- OutputStream <- 内存；

**范例：**实现一个小写字母转大写字母的操作

* 为了方便的实现字母的转大写的转换（避免不必要的字符也被转换了）可以借助于Character：
  * public static char toLowerCase(char ch)；
  * public static int toLowerCase(int codePoint)；
  * public static char toUpperCase(char ch)；
  * public static int toUpperCase(int codePoint)；

```java
package com.alpha;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		String str = "Hello World!"; // 要求被转换的字符串
		// 本次将通过内存操作流实现转换，先将数据保存在内存流里面，而后从里面取出每一个数据
		// 将所有要读取的数据设置到内存输入流之中，本次利用向上转型
		InputStream input = new ByteArrayInputStream(str.getBytes());
		// 为了能够将所有的内存流数据取出，可以使用ByteArrayOutputStream
		OutputStream output = new ByteArrayOutputStream();
		int temp = 0; // 读取每一个字节数据
		// 经过此次循环之后，所有的数据都将保存在内存输出流对象之中
		while ((temp = input.read()) != -1) { // 每次读取一个数据
			output.write(Character.toUpperCase(temp)); // 字节输出流
		}
		System.out.println(output); // 调用toString()方法
		input.close();
		output.close();
	}
}
```

以上的操作代码里面，所有的输入和输出流都发生了向上转型，向上转型的好处是可以得到操作模式的统一，但是千万不要忽略了一个问题，每一个子类实际上都有每一个子类自己的功能，在ByteArrayOutputStream类里面有一个非常重要的方法：public byte[] toByteArray()，这个方法可以将所有的保存在内存中的字节数据变为字节数组。

利用这个BYteArrayOutputStream可以实现多个文件的同时读取。

**范例：**实现文件的合并读取

```java
package com.alpha;
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
public class MainClass { 
	public static void main(String[] args) throws Exception {
		File filea = new File("d:" + File.separator + "infoa.txt");
		File fileb = new File("d:" + File.separator + "infob.txt");
		InputStream inputa = new FileInputStream(filea);
		InputStream inputb = new FileInputStream(fileb);
		ByteArrayOutputStream output = new ByteArrayOutputStream();
		int temp = 0; // 每次读取一个字节
		while ((temp = inputa.read()) != -1) {
			output.write(temp);
		}
		while ((temp = inputb.read()) != -1) {
			output.write(temp);
		}
		// 现在所有的内容都保存在了内存输出流里面，所有的内容变为字节数组取出
		byte[] data = output.toByteArray();
		output.close();
		inputa.close();
		inputb.close();
		System.out.println(new String(data));
	}
}
```

对于OutputStream和InputStream又有了新的输入及输出的位置，其中以ByteArrayOutputStream最为重要。

### 管道流

管道流主要的功能是实现两个线程之间的IO处理操作。管道字符流也分为两类：

* **字节管道流：**PipedOutputStream、PipedInputStream;
  * 连接：`public void connect(PipedInputStream snk) throws IOException`;
* **字符管道流：**PipedWriter、PipedReader;
  * 连接：`public void connect(PipedWriter src) throws IOException`;
  
**范例：**实现管道操作
```java
public class Application {
    public static void main(String[] args) throws IOException, InterruptedException {
        SendThread send = new SendThread();
        ReceiveThread receive = new ReceiveThread();
        send.getOutputStream().connect(receive.getInputStream());
        new Thread(send, "消息发送线程").start();
        new Thread(receive, "消息接收线程").start();
    }
}
class SendThread implements Runnable {
    private PipedOutputStream outputStream;
    public SendThread() {
        this.outputStream = new PipedOutputStream();
    }
    @Override
    public void run() {
        for (int i = 0; i < 10; i ++) {
            try {
                this.outputStream.write(("[第" + (i + 1) + "次信息发送 - " + Thread.currentThread().getName()+ "] Hello World!\n").getBytes());
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        try {
            this.outputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public PipedOutputStream getOutputStream() {
        return outputStream;
    }
}
class ReceiveThread implements Runnable {
    private PipedInputStream inputStream;
    public ReceiveThread() {
        this.inputStream = new PipedInputStream(); 
    }
    @Override
    public void run() {
        byte[] buf = new byte[1024];
        int len = 0;
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        try {
            while ((len = this.inputStream.read(buf)) != -1) {
                bos.write(buf, 0, len);
            }
            System.out.println("[" + Thread.currentThread().getName() + "接收消息] \n" + new String(bos.toByteArray()));
            bos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            this.inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public PipedInputStream getInputStream() {
        return inputStream;
    }
}
```

### RandomAccessFile

对于文件内容的处理操作主要通过InputStream（Reader）、OutputStream（Writer）来实现，但是利用这些类实现的内容读取只能够将数据部分部分读取进来。

为了弥补这种方式的不足，Java提供了RandomAccessFile类，这个类可以实现文件的跳跃式读取，方便读取固定格式的内容，实现类似C语言的文件访问形式。

RandomAccessFile类里面定义有如下的操作方法：
* 构造方法：`public RandomAccessFile(File file, String mode) throws FileNotFoundException`;
  * 文件处理模式：r、rw；

**范例：**实现文件的保存
```java
public class Application {
    public static void main(String[] args) throws Exception {
        File file = new File("d:" + File.separator + "hello.txt");
        RandomAccessFile raf = new RandomAccessFile(file, "rw");
        String[] names = new String[] {"zhangsan", "lisi    ", "wangwu  "};
        int[] ages = new int[] {30, 20, 16};
        for (int i = 0; i < names.length; i ++) {
            raf.write(names[i].getBytes());
            raf.write(ages[i]);
        }
        raf.close();
    }
}
```

RandomAccessFile最大的特点是在于数据的读取处理上，因为所有的数据是按照固定长度进行的保存，所以读取的时候就可以进行跳字节的读取：
* 跳过n个字节：`public int skipBytes(int n) throws IOException`;
* 定位到某个字节：`public void seek(long pos) throws IOException`;

**范例：**读取数据
```java
public class Application {
    public static void main(String[] args) throws Exception {
        File file = new File("d:" + File.separator + "hello.txt");
        RandomAccessFile raf = new RandomAccessFile(file, "rw");
        {
            // 读取王五的数据
            raf.skipBytes(24);
            byte[] data = new byte[8];
            int len = raf.read(data);
            System.out.println("姓名：" + new String(data, 0, len) + "、年龄：" + raf.readInt());
        }
        {
            // 读取李四的数据
            raf.seek(12);
            byte[] data = new byte[8];
            int len = raf.read(data);
            System.out.println("姓名：" + new String(data, 0, len) + "、年龄：" + raf.readInt());
        }
        {
            // 读取战三的数据
            raf.seek(0);
            byte[] data = new byte[8];
            int len = raf.read(data);
            System.out.println("姓名：" + new String(data, 0, len) + "、年龄：" + raf.readInt());
        }
        raf.close();
    }
}
```
