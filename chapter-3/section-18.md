## 网络编程

---

利用Java进行网络编程的时代基本上已经过去了。之所以讲解主要的目的是为了阐述网络操作的过程，因为这些过程会影响到之后的JavaEE的开发。

### 网络编程

网络：是将物理上分开的主机进行连接所形成的交换区域。那么所谓的网络编程值得的就是服务区段与客户端编程的开发操作实现。

但是在实际的工作下对于网络的编程有两种形式：

* 形式一：C/S结构（Client/Server），此类模式的开发一般要编写两套程序，一套是客户端代码，另外一套属于服务器代码，这样的程序开发非常的麻烦，因为要维护两套程序的使用，但是这类程序有一点好处：安全性高，因为使用的是自己的连接端口，并且使用的是自己的通讯协议。

* 形式二：B/S结构（Browser/Server），不再单独开发客户端代码，只开发一套服务器端程序，客户端将利用浏览器进行访问，这种模式只需要开发一套程序，但是安全性不高，因为使用的而是公共的HTTP协议，以及公共的80端口。

本次的网络编程值得就是C/S程序开发，即：要开发两套程序，并且这样的程序也可以称为Socket程序，而对于 C/S结构的程序分为两类：

* TCP程序：采用可靠的链接方式进行的传输；

* UDP程序：不可靠的链接，属于数据报协议。

### 开发第一个网络程序

如果要想进行网络程序的开发，那么最为核心的两个类：

* 服务器类：ServerSocket，主要工作在服务器端，用于接收用户的请求；

* 客户端类：Socket，每一个链接到服务区上的用户都通过Socket表示；

**范例：**定义服务器端 —— 主要使用ServerSocket

* 构造方法：public ServerSocket(int port) throws IOException，设置监听端口；

* 接收客户端连接：public Socket accept() throws IOException;

* 取得客户端的输出功能，Socket类定义方法：public OutputStream getOutputStream() throws IOException；

```java
package com.alpha;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;
public class HelloServer {
	public static void main(String[] args) throws Exception {
		ServerSocket server = new ServerSocket(9527); // 所有的服务器必须有端口
		System.out.println("等待客户端连接......");
		Socket client = server.accept(); // 等待客户端连接
		// OutputStream并不方便进行内容的输出，所以利用打印流完成输出
		PrintStream out = new PrintStream(client.getOutputStream());
		out.println("Hello World!");
		out.close();
		client.close();
		server.close();
	}
}
```

此时的服务器只是出处一个Hello World字符串而后就关闭服务器，只能够处理一次客户端的请求。

**范例：**编写客户端 —— Socket

* 构造方法：public Socket(String host, int port) throws UnknownHostException, IOException
  * host表示主机的IP地址，如果是本机直接访问那么使用localhost（127.0.0.1）代替IP；
* 得到输入数据：public InputStream getInputStream() throws IOException。

```java
package com.alpha;
import java.net.Socket;
import java.util.Scanner;
public class HelloClient {
	public static void main(String[] args) throws Exception {
		Socket client = new Socket("localhost", 9527); // 连接服务器端
		// 取得客户端的输入流对象，表示接收服务器端的输出信息
		Scanner scan = new Scanner(client.getInputStream());
		scan.useDelimiter("\n");
		if (scan.hasNext()) {
			System.out.println("[Echo] " + scan.next());
		}
		scan.close();
		client.close();
	}
}
```

客户端现在也只是连接一次服务器并且接收输入数据输出后结束操作。

### Echo程序

在网络编程之中ECHO是一个经典的程序开发模型，本程序的意义在于：客户端随意输入信息并且将信息发送给服务器端，服务器端接收后前面加上一个"ECHO"的标记返回。

本程序设计如下：

* 由于需要多次输入，所以不能够每次连接后立刻关闭服务端；
* 可以设置一个字符串，如果输入了"byebye"，那么表示结束本次的Echo操作。

**范例：**实现服务器端

```java
package com.alpha.demo;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Scanner;
public class EchoServer {
	public static void main(String[] args) throws Exception {
		ServerSocket server = new ServerSocket(9527);
		Socket client = server.accept(); // 连接客户端
		// 得到客户端输入数据以及向客户端输出数据的对象
		Scanner scan = new Scanner(client.getInputStream());
		PrintStream out = new PrintStream(client.getOutputStream());
		boolean flag = true; // 控制多次接收操作
		while (flag) {
			if (scan.hasNext()) {
				String str = scan.next().trim(); // 得到客户端发送的内容
				if ("bye".equalsIgnoreCase(str)) { // 程序要结束
					flag = false;
					out.println("bye.");
				} else { // 应该回应消息
					out.println("[ECHO] " + str);
				}
			}
		}
		scan.close();
		out.close();
		client.close();
		server.close();
	}
}
```

**范例：**定义客户端

```java
package com.alpha.demo;
import java.io.PrintStream;
import java.net.Socket;
import java.util.Scanner;
public class EchoClient {
	public static void main(String[] args) throws Exception {
		Socket client = new Socket("localhost", 9527);
		Scanner input = new Scanner(System.in); // 键盘输入数据
		Scanner scan = new Scanner(client.getInputStream());
		PrintStream out = new PrintStream(client.getOutputStream());
		input.useDelimiter("\n");
		scan.useDelimiter("\n");
		boolean flag = true;
		while (flag) {
			System.out.print("> ");
			if (input.hasNext()) {
				String str = input.next().trim();
				out.println(str); // 发送服务器端数据
				if ("bye".equalsIgnoreCase(str)) {
					flag = false; // 结束循环
				}
				if (scan.hasNext()) {
					System.out.println(scan.next().trim()); // 输出回应数据
				}
			}
		}
		input.close();
		scan.close();
		out.close();
		client.close();
	}
}
```

此时就是实现了一个最简单的服务器端与客户端通讯。

此时的程序只能连接一个客户端，而不能够连接其他客户端，因为所有的操作都是在主线程上进行的开发，也就是说此时的程序属于单线程的网络应用，实际中不可能如此进行，所以为了能够让一个服务器可以同时处理多个客户端的操作，使用多线程描述。把每一个连接到服务器端的客户都作为一个独立的线程对象保留。

**范例：**修改服务器端

```java
package com.alpha.demo;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Scanner;
class EchoThread implements Runnable {
	private Socket client = null;
	public EchoThread(Socket client) {
		this.client = client;
	}
	@Override
	public void run() {
		try {
			Scanner scan = new Scanner(client.getInputStream());
			PrintStream out = new PrintStream(client.getOutputStream());
			boolean flag = true; // 控制多次接收操作
			while (flag) {
				if (scan.hasNext()) {
					String str = scan.next().trim(); // 得到客户端发送的内容
					if ("bye".equalsIgnoreCase(str)) { // 程序要结束
						flag = false;
						out.println("bye.");
					} else { // 应该回应消息
						out.println("[ECHO] " + str);
					}
				}
			}
			scan.close();
			out.close();
			client.close();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
public class EchoServer {
	public static void main(String[] args) throws Exception {
		ServerSocket server = new ServerSocket(9527);
		boolean flag = true;
		while (flag) {
			Socket client = server.accept(); // 连接客户端
			new Thread(new EchoThread(client)).start();
		}
		server.close();
	}
}
```

服务开发的基础要素：网络支持类、IO、多线程。

#### 总结

对于网络编程首先明确的一点是：很少会去进行它的实际开发，了解一下基本概念就好。

