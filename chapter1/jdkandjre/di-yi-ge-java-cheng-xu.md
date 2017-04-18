在刚开始的编写代码过程中尽量使用记事本。

```java
Hello.java

public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

将文件保存至Hello.java。

Java程序分为两个操作：

* 编译程序：javac Hello.java ，此时会形成Hello.class文件，属于字节码文件。
* 解释程序：java Hello

Java程序的组成：

1、所有的Java程序都有一个最为核心的单元：类，本次的程序使用了一个Hello类，对于类有以下两种声明方式：

* public class 类名称 {} ：文件名称必须与类名称保持一致，一个\*.java文件中只能有一个public class定义；
* class 类名称 {} ：文件名称可以与类名称不一致，但是生成的\*.class是根据文件中定义的class名称一致的。在一个\*.java文件里面可以定义有多个class，但是编译后会分别形成不同的\*.class文件
* **严格的讲，在以后开发中，只允许在一个\*.java文件中出现一个public class定义。**

2、主方法：所有的程序都是由主方法开始执行的：

```java
public static void main(String[] args) {
    要编写的程序代码;
}
```





