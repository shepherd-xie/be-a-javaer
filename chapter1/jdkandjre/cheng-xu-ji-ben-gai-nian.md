## Java的注释

在程序之中，由于其基本组成都是代码，所以考虑到程序的可维护性特点，在编写代码的时候都要在每段代码上增加若干的说明文字，这些文字不需要被编译器编译，对于注释，Java一共分为三种形式：

* // 注释：单行注释；
* /\* ... \*/ ：多行注释；
* /\*\* ... \*/ ：文档注释。

```java
public class Hello {
    public static void main(String[] args) {
        // 单行注释
        /*
            多行注释
        */
        System.out.println("Hello World!");
    }
}
```

在日后的开发过程中，尽可能的使用单行注释，原因是：在一些开发工具里面，多行注释格式化后的效果不好。而对于文档注释，需要有开发工具的支持后才可以更好的编写。

## 标识符与关键字

之前曾经给过一个程序的基本结构：

```java
public class 类名称 {}
```

这里的类名称就属于一个标识符的内容，但是除了类名称之外，属性名称、方法名称等也都成为标识符。所有的标识符都有自己严格的定义要求，基本要求如下**：标识符由字母、数字、下划线（\_）、美元符号（$）所组成，其中不能以数字开头，不能是Java中的保留字（关键字）。**

* 在编写的时候尽量不要使用数字，命名尽量有意义；
* 对于“$”符号有特殊意义，不要使用；
* 例如：Student、Math都属于有意义的内容。

| abstract | assert | boolean | break | byte |
| :---: | :---: | :---: | :---: | :---: |
| case | catch | char | class | const |
| continue | default | do | double | else |
| enum | extends | final | finally | float |
| for | goto | if | implements | import |
| instanceof | int | interface | long | native |
| new | package | private | protected | public |
| return | strictfp | short | static | super |
| switch | synchronized | this | throw | throws |
| transient | try | void | volatile | while |

对于所有给出的关键字有如下的几点：

* Java有两个未使用到的关键字：goto、const；
* Java有三个特殊含义的标记：true、false、null；
* JDK 1.4 之后增加了assert关键字；
* JDK 1.5 之后增加了enum关键字；

对于程序开发，以上的要求实际上就够了。但是从JDK 1.7 开始增加了许多神奇的特性。

```java
public class 你好 {
	public static void main(String[] args) {
		int 年龄 = 10;
		System.out.println(年龄);
	}
}
```





