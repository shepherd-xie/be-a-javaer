## Java基本概念

---

### CLASSPATH环境属性

默认情况下，类都是从当前所在的目录中进行加载的，如果要想改变加载目录 ，就使用CLASSPATH。

设置CLASSPATH：

```
set CLASS_PATH=路径
```

这种方法在一次退出后设置就会失效，想要永久设置CLASSPATH需要在环境变量中进行配置：

`CLASS_PATH=.;`

由于将所有的字节码文件放置在统一目录下会不方便管理，所以最正确的加载方式还是要从本目录进行加载。

**面试题：**请解释PATH和CLASSPATH的区别？

* PATH：是属于操作系统属性，定义所有可执行程序的路径；
* CLASS\_PATH：是Java程序解释类文件时所使用的的加载路径。

CLASSPATH主要目的是定义类的加载路径，不管定义了多少个路径，一定要定义一个“.”（表示由当前所在目录进行类的加载）。

### Java的注释

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

### 标识符与关键字

之前曾经给过一个程序的基本结构：

```java
public class 类名称 {}
```

这里的类名称就属于一个标识符的内容，但是除了类名称之外，属性名称、方法名称等也都成为标识符。所有的标识符都有自己严格的定义要求，基本要求如下**：标识符由字母、数字、下划线（\_）、美元符号（$）所组成，其中不能以数字开头，不能是Java中的保留字（关键字）。**

* 在编写的时候尽量不要使用数字，命名尽量有意义；
* 对于“$”符号有特殊意义，不要使用；
* 例如：Student、Math都属于有意义的内容。

给出所有关键字：

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
* **所有关键字都不用背**

对于程序开发，以上的要求实际上就够了。但是从JDK 1.7 开始增加了许多神奇的特性。

```java
public class 你好 {        //类名称
    public static void main(String[] args) {
        int 年龄 = 10;    //变量名称
        System.out.println(年龄);    //输出内容
    }
}
```

Java本身是支持中文的，只不过从来没人这么做过。**不要这么做。**

Java每个版本都会有新特性，新特性保守使用。

