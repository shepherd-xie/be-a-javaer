## 类与对象

---

面向过程：指的是针对某一个问题单独提出解决方案以及代码开发。

面向对象：以一种组件化的形式进行代码的设计，这样开发出来的代码最大的好处就是可以重用。

在面向对象的程序里面包含有如下的几个特征：

* 封装性：保护内部的定义结构安全性；

* 继承性：在已有的程序结构上继续扩充新的内容；

* 多态性：指的是在某一个概念范围上的满足

而对于面向对象开发分为三个步骤：OOA（Object-Oriented Analysis，面向对象分析）、OOD（Object-Oriented Design，面向对象设计）、OOP（Object Oriented Programming，面向对象编程）。

### 认识类与对象

类与对象是整个面向过程之中最为基础的组成单元，所有的类实际上都是用来描述对象的结构，例如：每一个人的姓名、年龄、身高等一系列的因素。某一类的共性的集合对象，除了具备以上的特征（属性）之外，实际上还包括许多的行为（功能），所有根据此类产生出的对象都具备有相同的行为。对象所能够操作的行为都是由类类决定的，超过类的范畴的操作是不能够使用的。

类实际上是对象操作的模板，但是类不能够直接使用，必须通过实例对象来使用。

### 类与对象的基本定义

如果要在程序之中定义类可以使用“ class 类名称 {} ”的语法结构完成， 而类之中的组成主要有两点：

* FIeld（属性、变量、成员），就是一堆变量的集合；
* Method（方法、行为），之前见到的方法，但是此处的方法是由对象调用的。

**范例：**定义类

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
```

类现在虽然已经诞生了，但是如果要想使用类必须要有对象，对象的定义格式有如下两种：

* 声明并实例化对象：类名称 对象名称 = new 类名称\(\);
* 分步完成：
  * 声明对象：类名称 对象名称 = null;
  * 实例化对象：对象名称 = new 类名称\(\);

引用数据类型与基本数据类型最大的不同在于需要内存的开辟及使用，所以new关键字的主要功能就是开辟内存空间，即：只要是引用数据类型想使用，就必须使用new来开辟内存空间。

当一个对象实例化之后就可以使用如下的方式利用对象来操作类 结构：

* 对象.属性：表示要操作类中的属性内容；
* 对象.方法\(\)：表示要调用类中的方法。

**范例：**使用类 —— 在主类中使用Book类

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book bk = new Book();    //声明并实例化对象
        bk.title = "Java基础入门";    //操作属性内容
        bk.price = 89.9;        //操作属性内容
        bk.getInfo();            //调用类中的getInfo()方法
    }
}
```

如果想要对以上的程序进行内存分析，可以首先给出两块内存空间的概念：

* 堆内存：保存每一个对象的属性内容，对内存需要用关键字new才可以开辟；
* 栈内存：保存的是一块堆内存的地址，但是为了方便分析，可以简单地理解栈内存保存的是对象名字；

![](/assets/import 2.1-1.png)

对象除了可以利用一行语句定义之外，也可以分步完成。

**范例：**分步的方式实例化对象

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book bk = null;            //声明对象
        bk = new Book();        //实例化对象（开辟堆内存）
        bk.title = "Java基础入门";    //操作属性内容
        bk.price = 89.9;        //操作属性内容
        bk.getInfo();            //调用类中的getInfo()方法
    }
}
```

任何情况下只要有关键字new，都表示要开辟新的堆内存空间，一旦堆内存空间开辟了，里面就一定会有有所有类重定义的属性内容，当然所有的属性诶荣都是其对应数据类型的默认值。![](/assets/import 2.1-2.png)以上的代码是分为两步的方式实现了对象的实例化（开辟了堆内存的对象称为实例化对象），若果使用了没有实例化的对象。

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book bk = null;            //声明对象
        bk.title = "Java基础入门";    //操作属性内容
        bk.price = 89.9;        //操作属性内容
        bk.getInfo();            //调用类中的getInfo()方法
    }
}
```

此时由于使用了没有实例化的Book类实例对象，所以程序在运行的时候出现了“ NullPointerException ”（空指针异常），此类异常只要是引用数据类型都有可能出现。

### 引用数据的初步分析

引用是整个Java开发之中的核心精髓所在，即：只有掌握了引用这一基础概念之后才可以继续向后面的课程深入学习。引用类似于C/C++中的指针概念，但是要比指针更加简单。

在所有的引用分析里面，最关键的还是在于关键字“ new ”，一定要注意的是，每一次使用关键字new都一定会开辟新的堆内存空间，所以如果代码中声明了两个对象，并且使用了关键字new为两个对象分别进行对象的实例化操作，那么一定是各自占有各自的堆内存空间，并且不会相互影响。

**范例：**声明两个对象

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book bookA = new Book();
        Book bookB = new Book();
        bookA.title = "Java基础入门";
        bookA.price = 89.9;
        bookB.title = "操作系统";
        bookB.price = 58.8;
        bookA.getInfo();
        bookB.getInfo();
    }
}
```

![](/assets/import 2.1-3.png)这个时候给出的代码，是声明并实例化对象，用一行语句完成的，观察引用类型的使用。

**范例：**对象引用传递

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book bookA = new Book();    //声明并实例化对象
        Book bookB = null;            //声明对象
        bookA.title = "Java基础入门";
        bookA.price = 89.9;
        bookB = bookA;                //引用传递
        bookB.price = 58.8;
        bookA.getInfo();
        bookB.getInfo();
    }
}
```

严格来讲bookA和bookB里面保存的是对象的地址信息，所以以上的引用过程就属于将bookA的地址给了bookB。![](/assets/import 2.1-4.png)由于此时两个对象指向的是同一块堆内存空间，所以任何一个对象修改了堆内存中的数据后，都会影响到其他对象在引用的操作过程中，一块堆内存可以同时被多个栈内存所指向，但是一块栈内存只能保存一块堆内存空间的地址。

**范例：**继续观察引用传递

```java
class Book { //定义一个新的类
    String title; //书的名字
    double price; //书的价格
    public void getInfo() { //此方法将由对象调用
        System.out.println("图书名称：" + title + "，价格：" + price);
    }
}
public class MainClass {
    public static void main(String[] args) {
        Book bookA = new Book();    //声明并实例化对象
        Book bookB = null;            //声明对象
        bookA.title = "Java基础入门";
        bookA.price = 89.9;
        bookB.title = "操作系统";
        bookB.price = 58.8;
        bookB = bookA;                //引用传递
        bookB.price = 108.8;
        bookA.getInfo();
        bookB.getInfo();
    }
}
```

![](/assets/import 2.1-5.png)通过内存的分析可以发现，在引用数据类型关系里面，一块没有任何栈内存指向的堆内存空间就将成为垃圾，所有的垃圾会不定期的被垃圾收集器（GC）进行回收，回收后会释放掉其所占用的空间。

虽然在Java里面支持了自动的垃圾收集处理，但是在代码的开发过程之中应该尽量的减少垃圾空间的产生。

