## this关键字

---

this关键字是Java中比较复杂的一个关键字，因为this有一个核心概念：当前对象，而这个当前对象就很难去理解。

在Java里面，利用this关键字可以实现类属性的调用、类方法的调用、表示当前对象。

### 调用属性

下面来观察如下的一段代码。

**范例：**观察程序

```java
class Book {
    private String title;
    private double price;
    public Book(String t, double p) {
        title = t;
        price = p;
    }
    //setter、getter略
    public String getInfo() {
        return "书名：" + title + "，价格：" + price;
    } 
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book("Java基础入门", 89.8);
        System.out.println(book.getInfo());
    }
}
```

观察如下的一点：

```java
public Book(String t, double p) {
    title = t;
    price = p;
}
```

此处使用的是一个构造方法，而这个构造方法的主要功能是为title与price初始化使用，但是方法中的参数名称无法

起到“见名知意”的效果。既然构造方法中的参数的目的是为了类中的属性初始化，那么最好的做法是直接将参数设

置为与属性名称保持一致。

```java
public Book(String title, double price) {
    title = title;
    price = price;
}
```

修改为以上的形式参数就比较合理，但是最终发现在构造方法传递的参数内容并没有传递到属性之中。

在Java程序里面它是以“{}”为界限。如果现在属性名称与参数名称出现了重名的情况下，默认情况如果没有加入任

何的限制，值得都是最近的“{}”内的变量名称。所以在这种情况下为了可以明确的找到要访问的变量属于类中的属

性的时候，需要在变量前加上this，这样就可以准确的进行属性的标记。

```java
class Book {
    private String title;
    private double price;
    public Book(String title, double price) {
        this.title = title;
        this.price = price;
    }
    //setter、getter略
    public String getInfo() {
        return "书名：" + this.title + "，价格：" + this.price;
    } 
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book("Java基础入门", 89.8);
        System.out.println(book.getInfo());
    }
}
```

在以后程序开发之中，只要是访问类中的属性前面必须加上“this.”。

### 调用方法

通过之前的代码可以发现，所谓的this实际上指的就是本类的结构，在一个类中除了有成员之外还会有方法（普通方法、构造方法），利用this可以调用本类的普通方法或构造方法。

1、调用普通方法

```java
class Book {
    private String title;
    private double price;
    public Book(String title, double price) {
        this.title = title;
        this.price = price;
    }
    //setter、getter略
    public void print() {
        System.out.println("**********");
    }
    public String getInfo() {
        this.print();
        return "书名：" + this.title + "，价格：" + this.price;
    } 
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book("Java基础入门", 89.8);
        System.out.println(book.getInfo());
    }
}
```

调用普通方法的时候是否在方法前加入“this.”并没有一个明确的要求，即使不加this也表示本类调用的方法，但是从代码的严谨性上来讲一定要加上this。

在一个类里面方法除了普通方法还会包含构造方法，这是表示多个方法之间要进行互相调用。使用的形式“this\(参数,参数,...\)”。

下面将通过一个简短的代码来分析，为什么需要构造方法间的相互调用。

**范例：**观察问题

现在定义的Book类里面有三个构造方法，但是要求不管调用哪一个构造方法都要输出一行提示信息“新的Book类对象生成”。

```java
class Book {
    private String title;
    private double price;
    public Book() {
        System.out.println("一个新的Book类对象生成");
    }
    public Book(String title) {
        System.out.println("一个新的Book类对象生成");
        this.title = title;
    }
    public Book(String title, double price) {
        System.out.println("一个新的Book类对象生成");
        this.title = title;
        this.price = price;
    }
    //setter、getter略
    public String getInfo() {
        return "书名：" + this.title + "，价格：" + this.price;
    } 
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book("Java基础入门", 89.8);
        System.out.println(book.getInfo());
    }
}
```

此时代码里面出现有重复代码，必须想办法消除重复。

```java
class Book {
    private String title;
    private double price;
    public Book() {
        System.out.println("一个新的Book类对象生成");
    }
    public Book(String title) {
        this();
        this.title = title;
    }
    public Book(String title, double price) {
        this(title);
        this.price = price;
    }
    //setter、getter略
    public String getInfo() {
        return "书名：" + this.title + "，价格：" + this.price;
    } 
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book("Java基础入门", 89.8);
        System.out.println(book.getInfo());
    }
}
```

虽然以上实现了构造方法间的相互调用，但是依然会存在一些限制：

* 使用“this\(\)”调用构造方法形式的代码只能够放在构造方法的首行
* 进行构造方法相互调用的时候，一定要保留调用的出口。

**范例：**观察错误的代码

```java
class Book {
    private String title;
    private double price;
    public Book() {
        this("Java", 89.8);
        System.out.println("一个新的Book类对象生成");
    }
    public Book(String title) {
        this();
        this.title = title;
    }
    public Book(String title, double price) {
        this(title);
        this.price = price;
    }
    //setter、getter略
    public String getInfo() {
        return "书名：" + this.title + "，价格：" + this.price;
    } 
}
public class MainClass {
    public static void main(String[] args) {
        Book book = new Book("Java基础入门", 89.8);
        System.out.println(book.getInfo());
    }
}
```

以上代码在编译之后就会出现错误提示“构造方法递归调用”，所以在使用this\(\)互相调用构造的时候至少保留一个构造没有使用this\(\)调用其他构造方法。

**实例：**定义一个雇员类（编号、姓名、工资、部门），在这个类里面提供四个构造方法：

* 无参构造：编号为0，姓名为无雇员，工资为0.0，部门为未定；
* 单参构造（传递编号）:姓名为临时工，工资为800.0，部门为后勤部；
* 双参构造（传递编号、姓名）：工资为2000.0，部门为技术部；
* 四参构造。

**实现方式一：**按照传统的风格实现

```java
class Emp {
    private int empno;
    private String ename;
    private double sal;
    private String dept;
    public Emp() {
        this.empno = 0;
        this.ename = "无雇员";
        this.sal = 0.0;
        this.dept = "未定";
    }
    public Emp(int empno) {
        this.empno = empno;
        this.ename = "临时工";
        this.sal = 800.0;
        this.dept = "后勤";
    }
    public Emp(int empno, String ename) {
        this.empno = empno;
        this.ename = ename;
        this.sal = 2000.0;
        this.dept = "技术部";
    }
    public Emp(int empno, String ename, double sal, String dept) {
        this.empno = empno;
        this.ename = ename;
        this.sal = sal;
        this.dept = dept;
    }
    //setter、getter略
    public String getInfo() {
        return "雇员编号：" + this.empno + "，名称：" + this.ename + "，工资：" + this.sal + "，部门：" + this.dept;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Emp ea = new Emp();
        Emp eb = new Emp(9527);
        Emp ec = new Emp(7566, "ALLEN");
        Emp ed = new Emp(7869, "KING", 5000.0, "Boss");
        System.out.println(ea.getInfo());
        System.out.println(eb.getInfo());
        System.out.println(ec.getInfo());
        System.out.println(ed.getInfo());
    }
}
```

虽然以上的代码完成了功能，但是却存在有重复的操作，很明显，这种代码不符合实际的开发要求。

**实现方式二：**利用构造方法简化代码

```java
class Emp {
    private int empno;
    private String ename;
    private double sal;
    private String dept;
    public Emp() {
        this(0, "无雇员", 0.0, "未定");
    }
    public Emp(int empno) {
        this(empno, "临时工", 800.0, "后勤");
    }
    public Emp(int empno, String ename) {
        this(empno, ename, 2000.0, "技术部");
    }
    public Emp(int empno, String ename, double sal, String dept) {
        this.empno = empno;
        this.ename = ename;
        this.sal = sal;
        this.dept = dept;
    }
    //setter、getter略
    public String getInfo() {
        return "雇员编号：" + this.empno + "，名称：" + this.ename + "，工资：" + this.sal + "，部门：" + this.dept;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Emp ea = new Emp();
        Emp eb = new Emp(9527);
        Emp ec = new Emp(7566, "ALLEN");
        Emp ed = new Emp(7869, "KING", 5000.0, "Boss");
        System.out.println(ea.getInfo());
        System.out.println(eb.getInfo());
        System.out.println(ec.getInfo());
        System.out.println(ed.getInfo());
    }
}
```

此时的操作就利用了构造方法间的相互调用解决了代码的重复问题。

### 表示当前对象

当前对象指的是当前正在调用类中方法的对象。

```java
class Book {
	public void print() {
		//this就是当前调用方法的对象
		System.out.println("this = " + this);
	}
}
public class MainClass {
	public static void main(String[] args) {
		Book booka = new Book();
		Book bookb = new Book();
		System.out.println("booka = " + booka);
		booka.print();
		System.out.println("----------------------------");
		System.out.println("bookb = " + bookb);
		bookb.print();
	}
}
```

之前出现的“this.属性”实际上指的就是当前对象中的属性，一定是保存在堆内存中的内容。

**思考：**观察如下代码的执行

```java
class A {
	private B b;
	//2、执行A()
	public A() {
		//3、实例化B类对象
		this.b = new B(this); //4、this == temp
		this.b.get(); //7、调用b.get()
	}
	//10、调用print()
	public void print() {
		System.out.println("Hello World !");
	}
}
class B {
	private A a;
	//5、调用B(A a)，a == temp
	public B(A a) {
		this.a = a; //6、保存a对象（temp）
	}
	//8、调用this.a（temp）.print()
	public void get() {
		this.a.print();
	}
}
public class MainClass {
	public static void main(String[] args) {
		//1、实例化A类对象，调用A()
		A temp = new A();
	}
}
```

#### 总结

1、类中的属性调用一定要加上this；

2、类中的构造方法间的互相调用一定要保留出口；

3、this表示当前对象，指的是当前正在调用类中方法的对象。

