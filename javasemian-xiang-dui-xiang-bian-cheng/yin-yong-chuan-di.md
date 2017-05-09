## 引用传递

---

引用传递是整个Java的精髓所在，下面将利用三个程序对引用传递做进一步的分析。

引用传递的核心意义：同一块堆内存空间可以被不同的栈内存所指向，不同栈内存可以对同一堆内存进行内容的修改。

**范例一：**

```java
class Message {
	private int num = 10;
	public Message(int num) {
		this.num = num;
	}
	public void setNum(int num) {
		this.num = num;
	}
	public int getNum() {
		return this.num;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Message msg = new Message(30);
		fun(msg);
		System.out.println(msg.getNum());
	}
	public static void fun(Message temp) {
		temp.setNum(100);
	}
}
```



