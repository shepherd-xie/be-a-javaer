## 内部类

---

内部类的基本定义结构

* 使用static定义内部类
* 在方法中定义内部类

从开发的角度，内部类能少用就少用。优先考虑的还是普通类。

### 基本概念

所谓的内部类指的就是在一个类的内部继续定义了其他内部结构类的情况。

**范例：**观察内部类的基本形式

```java
class Outer { // 外部类
	private String msg = "Hello World !";
	class Inner { // 定义了一个内部类
		public void print() {
			System.out.println(msg);
		}
	}
	public void fun() {
		// 实例化内部类对象，并且调用print()方法
		new Inner().print();
	}
}
public class MainClass {
	public static void main(String[] args) {
		Outer out = new Outer(); // 实例化外部类对象
		out.fun(); // 调用外部类方法
	}
}
```

以上类的代码并不麻烦，但是从结构上讲是存在问题的，因为从类的结构本身就意味着属性与方法的组成，可是一个类里面又定义了另外一个类。这个时候内部类牺牲了类的结构，可是它实现了一个很重要的目的。

为了观察内部类达到的目的，下面将之前的内部类拿到外面来，并且要求实现与之完全一样的功能。

