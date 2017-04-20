## Java开发准备

---

### JDK&JRE

JDK（Java Development Kit，Java开发工具包）：JDK是Java语言的软件开发工具包，JDK是整个java开发的核心，它包含了JAVA的运行环境，JAVA工具和JAVA基础的类库。

JRE（Java Runtime Environment，Java运行环境）：运行JAVA程序所必须的环境的集合，包含JVM标准实现及Java核心类库。即，如果你的电脑上没有JDK但是有JRE也是可以运行Java程序，但是不能进行开发。

### JDK的安装及配置

要想进行Java的开发就安装JDK。安装时（包括以后的一些软件）尽量在关闭安全卫士的状态下进行。

[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

进行Java开发最重要的是javac和java两个命令，但是由于这两个命令不是电脑自身提供的而是安装JDK 之后之后才有的，所以在使用的时候要进行环境变量的配置。

安装JDK后需要配置环境变量（1.8版本，默认安装路径）：

`JAVA_HOME=C:\Program Files\Java\jdk1.8.0_101;`

`CLASS_PATH=.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;`

`Path=;%JAVA_HOME%\bin;`

### 第一个Java程序

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

3、屏幕输出：

* 输出之后增加换行：System.out.println\(内容\);
* 输出之后不增加换行：System.out.print\(内容\);



