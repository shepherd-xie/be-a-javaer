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

![](/images/chapter-2/section-7/1.png)**范例二：**

```java
public class MainClass {
    public static void main(String[] args) {
        String msg = "Hello";
        fun(msg);
        System.out.println(msg);
    }
    public static void fun(String temp) {
        temp = "World";
    }
}
```

要注意的只有一点，String类对象的内容一旦声明，对象内容的改变依靠的是引用地址的改变。

![](/images/chapter-2/section-7/2.png)**范例三：**

```java
class Message {
    private String info = "nihao";
    public Message(String info) {
        this.info = info;
    }
    public void setInfo(String info) {
        this.info = info;
    }
    public String getInfo() {
        return this.info;
    }
}
public class MainClass {
    public static void main(String[] args) {
        Message msg = new Message("Hello");
        fun(msg);
        System.out.println(msg);
    }
    public static void fun(Message temp) {
        temp.setInfo("World");
    }
}
```

![](/images/chapter-2/section-7/3.png)

但是以上的内存描述并不严格，因为在程序里面，info也是一个String类的对象，所以如果想要描述清楚，应该使用如下的内存关系图。

![](/images/chapter-2/section-7/4.png)

虽然String属于类，属于引用类型，但是由于其内容不可改变的特点，很多时候就直接把String当成基本数据类型那样使用。也就是说，每一个String类型只能够保存一个数据。

### 引用传递的实际应用

假设现有一需求，每个人有一辆车或者没有车，要求设计满足以上数据存储的数据库。创建语句：

应该设计两张表：人员表，汽车表；

```
CREATE TABLE member (
    mid        NUMBER ,
    name        VARCHAR2(50) ,
    CONSTRAINT member_pk_mid PRIMARY KEY(mid)
) ;
CREATE TALBE car (
    mid        NUMBER ,
    pname        VARCHAR2(50) ,
    CONSTRAINT car_fk_mid FOREIGN KEY(mid) REFERENCES member(mid) ,
    CONSTRAINT car_pk_mid PRIMARY KEY(mid)
) ;
```

如果想要定义Java类，那么也应该定义两个类，所有通过分析可以发现。

简单Java类的编写原则：

* 类名称 = 表名称；
* 属性名称\(类型\) = 表字段\(类型\)；
* 一个实例化对象 = 一条记录；
* 多个实例化对象 = 多条记录；
* 引用关系 = 外键约束；

**范例：** 代码设计如下

```java
class Member {
    private int mid;
    private String name;
}
class Car {
    private String pname;
}
```

以上实现了两个类，并且没有外键关系，但是在表中可以通过外键描述关系，那么在类中可以采用引用来描述关系。

```java
class Member {
    private int midl;
    private String name;
    // car有实例化对象表示有车
    // car为null表示没有车
    private Car car;    // 表示属于人的车
}
class Car {
    private Member member;    // 车属于一个人
    private String pname;
}
```

需要注意的是Member和Car应该都是一个独立的对象，而后通过产生对象发生关系。

```java
class Member {
    private int mid;
    private String name;
    // car有实例化对象表示有车
    // car为null表示没有车
    private Car car;    // 表示属于人的车
    public Member(int mid, String name) {
        this.mid = mid;
        this.name = name;
    }
    public String getInfo() {
        return "mid=" + this.mid + ", name=" + this.name;
    }
}
class Car {
    private Member member;    // 车属于一个人
    private String pname;
    public Car(String pname) {
        this.pname = pname;
    }
    public String getInfo() {
        return "pname=" + this.pname;
    }
}
```

当Member或Car类的对象产生之后，那么就应该为这两个对象设置彼此的关系。

```java
class Member {
    private int mid;
    private String name;
    // car有实例化对象表示有车
    // car为null表示没有车
    private Car car;    // 表示属于人的车
    public Member(int mid, String name) {
        this.mid = mid;
        this.name = name;
    }
    public String getInfo() {
        return "mid=" + this.mid + ", name=" + this.name;
    }
    public void setCar(Car car) {
        this.car = car;
    }
    public Car getCar() {
        return this.car;
    }
}
class Car {
    private Member member;    // 车属于一个人
    private String pname;
    public Car(String pname) {
        this.pname = pname;
    }
    public String getInfo() {
        return "pname=" + this.pname;
    }
    public void setMember(Member member) {
        this.member = member;
    }
    public Member getMember() {
        return this.member;
    }
}
```

以上的程序类完成之后，下面需要对程序进行测试，但是程序的测试要求分两步;

* 第一步：根据定义的结构关系设置数据；
* 第二步：根据定义的结构关系取出数据；

```java
public class MainClass {
    public static void main(String[] args) {
        // 第一步：设置数据
        Member m = new Member(1, "Alpha");    // 独立对象
        Car c = new Car("MK14L");    // 独立对象
        m.setCar(c);    // 一个人有一辆车
        c.setMember(m); // 一辆车属于一个人
        // 第二步：取出关系
        // 通过人找到车的信息
        System.out.println(m.getCar().getInfo());
        // 通过车找到人的信息
        System.out.println(c.getMember().getInfo());
    }
}
```

下面可以进一步设计，例如：每个人都有自己的孩子，孩子还可能有车，那么有两种设计方法：

* 方法一：设计一个孩子类，
* 方法二：一个人的孩子本质还是一个人，与人的类没有区别，可以在Member类里面设计一个属性表示孩子，其类型就是Member。

```java
class Member {
    private int mid;
    private String name;
    private Member child;    // 表示孩子
    // car有实例化对象表示有车
    // car为null表示没有车
    private Car car;    // 表示属于人的车
    public Member(int mid, String name) {
        this.mid = mid;
        this.name = name;
    }
    public String getInfo() {
        return "mid=" + this.mid + ", name=" + this.name;
    }
    public void setChild(Member child) {
        this.child = child;
    }
    public Member getChild() {
        return this.child;
    }
    public void setCar(Car car) {
        this.car = car;
    }
    public Car getCar() {
        return this.car;
    }
}
class Car {
    private Member member;    // 车属于一个人
    private String pname;
    public Car(String pname) {
        this.pname = pname;
    }
    public String getInfo() {
        return "pname=" + this.pname;
    }
    public void setMember(Member member) {
        this.member = member;
    }
    public Member getMember() {
        return this.member;
    }
}
public class MainClass {
    public static void main(String[] args) {
        // 第一步：设置数据
        Member m = new Member(1, "Alpha");    // 独立对象
        Member chd = new Member(2, "Beta");    // 一个孩子
        Car c = new Car("MK14L");    // 独立对象
        Car cc = new Car("K98");    // 一辆车
        m.setCar(c);    // 一个人有一辆车
        c.setMember(m); // 一辆车属于一个人
        chd.setCar(cc); //一个孩子有一辆车
        cc.setMember(chd);    // 一个车属于一个孩子
        m.setChild(chd);    // 一个人有一个孩子
        // 第二步：取出关系
        // 通过人找到车的信息
        System.out.println(m.getCar().getInfo());
        // 通过车找到人的信息
        System.out.println(c.getMember().getInfo());
        // 通过人找到孩子的信息
        System.out.println(m.getChild().getInfo());
        // 通过人找打他孩子的车的信息
        System.out.println(m.getChild().getCar().getInfo());
    }
}
```

这样的操作在现实中十分常见。我们还可以针对引用做进一步的描述，例如:要求描述电脑，例如电脑由主机、显示器、键盘、CPU、内存、硬盘、显卡组成，那么如何通过代码描述？

```java
class 键盘 {}
class 鼠标 {}
class CPU {}
class 硬盘 {}
class 内存 {}
class 显示器 {}
class 主板 {}
class 主机 {
    private CPU[] 对象;
    private 硬盘[] 对象;
    private 主板 对象;
    private 内存 对象;
}
class 电脑 {
    private 主机 对象;
    private 显示器[] 对象;
    private 键盘 对象;
    private 鼠标 对象;
}
```

这样的设计思路在Java之中称为合成设计模式。

