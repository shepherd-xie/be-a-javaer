## Annotation

---

1、Annotation的作用；

2、三种内置的Annotation的使用。

对于软件程序的开发实际上经过了三个发展过程：

* 第一个过程：将所有与配置相关的内容和直接写到代码之中；
* 第二个过程：将配置与程序代码独立。即：程序运行的时候根据配置文件进行操作；
  * 最严重的问题：一个项目里面配置文件过多，根本就无法查询错误；
* 第三个过程：配置信息对用户而言无用，而且胡乱的修改还会导致程序的错误，所以可以将配置信息写回到程序里面，但是利用一些明显的标记来区分配置信息与程序。

Annotation是JDK1.5最大的特色，利用注解的形式来实现程序的不同功能实现。

在JavaSE里面支持自定义Annotation的开发，并且提供了三个最为常用的基础的Annotation：@Override、@Deprecated、@SuppressWarnings。

### 精确覆写：@Override

如果在输出对象的时候希望其可以返回需要的内容，那么一定要进行toString()方法的覆写。

**范例：**有没有可能在开发中出现以下的形式

```java
class Book {
	public String tostring() { // 原本打算覆写toString()
		return "这是一本书！";
	}
}
```

这个时候很明显并没有成功的进行toString()方法的覆写。但是这个错误无法在编译的时候发现，只能在程序运行的时候发现。

所以此时为了告诉编译器，toString()是覆写的方法，那么就可以加上“@Override”，明确的告诉编译器，这个方法应该是覆写来的，如果不是要报错。

```java
class Book {
	@Override // 只要正确进行了覆写，那么就不会出现编译的语法错误
	public String toString() { // 原本打算覆写toString()
		return "这是一本书！";
	}
}
```

如果是使用Eclipse编写代码，几乎都会自动生成这样的注解。

### 过期声明：@Deprecated

如果说现在有一个专门负责完成某项功能的工具包，里面有一个Hello类，在Hello类里面有一个fun()方法，在所有项目最初的发展阶段，fun()方法非常的完善，但是后来随着开发技术的不断将强，发现fun()这个方法的功能不足，这个时候对于开发者有两个选择：

* 选择一：直接在新版本的工具包里面取消掉fun()方法，同时直接给出新的fun2()方法；
* 选择二：在新版本的开发包里面保存fun()方法，但是通过某种途径告诉新的开发者，此方法有问题，并且提供fun2()这个新的方法供开发者使用。

很明显，第二种比较合适，因为第二种的做法可以兼顾已使用项目的情况。

这个时候就可以使用“@Deprecated”来进行声明。

**范例：**声明过期操作

```java
package com.alpha;
class Book {
	@Deprecated
	public void fun() {}
}
public class MainClass {
	public static void main(String[] args) {
		new Book().fun();
	}
}
```

利用此操作可以很好的实现方法功能的新旧交替。

### 压制警告：@SuppressWarnings

可以压制多个警告。

**范例：**观察警告

```java
package com.alpha;
class Book<T> {
	private T title;
	public void setTitle(T title) {
		this.title = title;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Book book = new Book();
		book.setTitle("Hello");
	}
}
```

如果说现在是开发者故意留下的警告信息，但是又不希望其总是提示警告。就可以选择压制警告。

```java
package com.alpha;
class Book<T> {
	private T title;
	public void setTitle(T title) {
		this.title = title;
	}
	public T getTitle() {
		return title;
	}
}
public class MainClass {
	@SuppressWarnings({ "rawtypes", "unchecked" })
	public static void main(String[] args) {
		Book book = new Book();
		book.setTitle("Hello");
	}
}
```

现在相当于取消了警告的提示消息。

#### 总结

1、可以发现如果使用了开发工具，所有的Annotation实际上都不需要我们去编写；

2、清楚三个Java的Annotation作用。

