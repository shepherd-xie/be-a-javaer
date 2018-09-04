## 覆写

---

1、方法的覆写；

2、属性的覆盖。

继承性的主要特征是子类可以根据父类已有的功能进行功能的拓展，但是在子类定义属性或方法的时候有可能定义的属性和方法与父类相同，那么在这样的情况下就称为覆写。

### 方法的覆写

当子类定义了与父类方法名相同、参数的类型以及个数、返回值相同的方法是就称发生了覆写。

**范例：** 观察覆写的产生效果

```java
class A {
	public void fun() {
		System.out.println("A类中的fun方法。");
	}
}
class B extends A {}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.fun();
	}
}
```

因为这个时候子类并没有fun\(\)方法，所以这个时候子类使用fun\(\)方法时会直接使用A类继承而来的fun\(\)方法。

**范例：** 观察覆写

```java
class A {
	public void fun() {
		System.out.println("A类中的fun方法。");
	}
}
class B extends A {
	public void fun() { // 此处为覆写
		System.out.println("B类中的fun方法。");
	}
}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.fun();
	}
}
```

当发生了覆写之后，此时会调用实例化子类中已经被覆写的方法。

一个类可能会产生多个子类，那么每个子类都一定会有自己的实现。

```java
class A {
	public void fun() {
		System.out.println("A类中的fun方法。");
	}
}
class B extends A {
	public void fun() { // 此处为覆写
		System.out.println("B类中的fun方法。");
	}
}
class C extends A {
	public void fun() { // 此处为覆写
		System.out.println("C类中的fun方法。");
	}
}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.fun();
		C c = new C();
		c.fun();
	}
}
```

**覆写的结果分析要素：**

* 观察实例化的是那个类；
* 观察这个实例化的类里面调用的方法是否已经被覆写过的，如果没覆写过调用父类。

**覆写的使用原则：**

如果在发现父类中的方法名称功能不足（不适合于本子类），但是又必须使用这个方法名称的时候，就需要采用覆写这一概念实现。

以上的代码确实已经实现了覆写的功能，但是如果想要更好的实现覆写的操作，还必须考虑到权限问题。_**被子类所覆写的方法不能够拥有比父类更严格的访问控制权限。**_

对于访问控制权限已经学习过三个：public&gt;default&gt;privete，也就是说private的访问权限是最严格的，即：如果父类的方法使用的是public声明，那么子类覆写此方法的时候只能是public，如果父类的方法是default的时候，那么子类覆写方法的时候只能使用default或public。_**99%的情况下方法都用public声明。**_

**范例：** 正确的覆写

```java
class A {
	public void fun() {
		System.out.println("A类中的fun方法。");
	}
}
class B extends A {
	public void fun() { // 此处为覆写
		System.out.println("B类中的fun方法。");
	}
}
```

```java
class A {
	void fun() {
		System.out.println("A类中的fun方法。");
	}
}
class B extends A {
	public void fun() { // 此处为覆写
		System.out.println("B类中的fun方法。");
	}
}
```

**范例：** 错误的覆写

```java
class A {
	public void fun() {
		System.out.println("A类中的fun方法。");
	}
}
class B extends A {
	void fun() { // 此处为覆写
		System.out.println("B类中的fun方法。");
	}
}
```

此时子类中使用了default权限，相对于父类中的public权限属于更加严格的范围。

**疑问？**如果父类方法中使用了private声明，子类覆写时使用了public声明，那么这个方法属于覆写么？

从概念上来讲，父类的方法是private，属于小范围权限，而public属于大范围权限。权限上符合于覆写要求。

下面先抛开权限的关系来看一个正常的覆写操作。

```java
class A {
	public void fun() {
		print();
	}
	public void print() {
		System.out.println("A类中的print()方法。");
	}
}
class B extends A {
	public void print() { // 此处为覆写
		System.out.println("B类中的print()方法。");
	}
}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.fun();
	}
}
```

下面使用private声明父类中的方法。

```java
class A {
	public void fun() {
		print();
	}
	private void print() {
		System.out.println("A类中的print()方法。");
	}
}
class B extends A {
	public void print() { // 此处为覆写
		System.out.println("B类中的print()方法。");
	}
}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.fun();
	}
}
```

这个时候发现子类中根本就没有覆写print\(\)方法，也就是说如果使用了private声明，那么这个方法对子类而言是不可见的，就算子类定义了一个与之完全相同的符合于覆写要求的方法，那么也不能够发生覆写。这个时候子类所写的方法实际上就相当于子类自己定义了一个新的方法而已。

一旦有了覆写之后会发现，默认情况下子类所能够调用的一定是被覆写过的方法。为了能够明确的由子类调用父类中已经被覆写的方法，那么可以使用“super.方法\(\)”来进行调用。

```java
class A {
	public void print() {
		System.out.println("A类中的print()方法。");
	}
}
class B extends A {
	public void print() { // 此处为覆写
		super.print();
		System.out.println("B类中的print()方法。");
	}
}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.print();
	}
}
```

> ### 关于子类继承父类私有成员的问题
> 
> JDK官方文档的解释:
> 
> > A subclass does not inherit the private members of its parent class. However, if the superclass has public or protected methods for accessing its private fields, these can also be used by the subclass.
> 
> Google翻译：
> 
> > 子类不继承其父类的私有成员。但是，如果超类具有访问其私有字段的公共或受保护方法，则子类也可以使用这些方法。
> 
> **从继承的概念来说**，private和final不被继承。Java官方文档上是这么说的。
> 
> **从内存的角度来说**，父类的一切都被继承(从父类构造方法被调用就知道了，因为new一个对象，就会调用构造方法，子类被new的时候就会调用父类的构造方法，所以从内存的角度来说，子类拥有一个完整的父类)。**子类对象所引用的内存有父类变量的一份拷贝。**
> 
> 以上内容仅用于引申，这个问题本身就是一个有歧义的问题。如果非要对这个问题寻求一个最终答案的话，可以参考Oracle的Java语言规范：
> 
> 引用自[Java语言规范\[The Java® Language Specification\]](https://docs.oracle.com/javase/specs/jls/se8/html/index.html) [8.2. Class Members](https://docs.oracle.com/javase/specs/jls/se8/html/jls-8.html#jls-8.2)
> 
> > Members of a class that are declared private are not inherited by subclasses of that class.
> > 
> > Only members of a class that are declared protected or public are inherited by subclasses declared in a package other than the one in which the class is declared.
> 
> Google翻译：
> > 
> > 声明为私有的类的成员不会被该类的子类继承。
> > 
> > 只有声明为protected或public的类的成员才能被继承于除了声明该类的包之外的其他包。
> 
> _**子类不能继承父类的私有成员，不能够通过正常的OOP去访问，但并不表示其在子类之中不存在。**_
> 
> 关于以上问题的验证，可以在之后的_反射_章节学习到相关知识。

**关于super.方法\(\)与this.方法\(\)的区别？**

使用“this.方法\(\)”会首先查找本类中是否存在要有调用的方法名称，如果存在则直接调用，如果不存在则查找父类中是否具备有此方法，如果有就调用，没有就会发生编译时错误；

使用“super.方法\(\)”，明确表示调用的不是本类的方法（不在本类中查询），而是直接调用父类的指定方法。

**面试题：**请解释重载与覆写的区别？（请解释Overloading与Overriding的区别？）
在使用Overloading的时候返回值能否不同？

| No. | 区别 | 重载 | 重写 |
| :---: | :---: | :---: | :---: |
| 1 | 英文单词 | Overloading | Overriding |
| 2 | 发生范围 | 发生在一个类里面 | 发生在继承关系中 |
| 3 | 定义 | 方法名称相同、参数的类型及个数不同 | 方法名称相同、参数的类型、个数相同、方法返回值相同 |
| 4 | 权限 | 没有权限的限制 | 被覆写的方法不能拥有比父类更加严格的访问控制权限 |

在发生重载关系的时候，返回值可以不同，但是考虑到程序设计的统一性，重载时尽量保证方法的返回值类型相同。

> ### 重写\(Overriding\)
> 
> 重写是子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。即_**外壳不变，核心重写！**_
> 
> 重写的好处在于子类可以根据需要，定义特定于自己的行为。 也就是说子类能够根据需要实现父类的方法。
> 
> ### 重载\(Overloading\)
> 
> 重载是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。
> 
> 每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。
> 
> #### 方法的重写规则
> 
> * 参数列表必须完全与被重写方法的相同；
> * 返回类型必须完全与被重写方法的返回类型相同；
> * 访问权限不能比父类中被重写的方法的访问权限更低。例如：如果父类的一个方法被声明为public，那么在子类中重写该方法就不能声明为protected。
> * 父类的成员方法只能被它的子类重写。
> * 声明为final的方法不能被重写。
> * 声明为static的方法不能被重写，但是能够被再次声明。
> * 子类和父类在同一个包中，那么子类可以重写父类所有方法，除了声明为private和final的方法。
> * 子类和父类不在同一个包中，那么子类只能够重写父类的声明为public和protected的非final方法。
> * 重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。
> * 构造方法不能被重写。
> * 如果不能继承一个方法，则不能重写这个方法。
>
> #### 方法的重载规则
> 
> * 被重载的方法必须改变参数列表(参数个数或类型或顺序不一样)；
> * 被重载的方法可以改变返回类型；
> * 被重载的方法可以改变访问修饰符；
> * 被重载的方法可以声明新的或更广的检查异常；
> * 方法能够在同一个类中或者在一个子类中被重载。
> * 无法以返回值类型作为重载函数的区分标准。
>
> 方法的重写(Overriding)和重载(Overloading)是java多态性的不同表现，重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式。

### 属性的覆盖

如果说现在子类定义了和父类完全相同的属性名称的时候，就称为属性的覆盖。

**范例：** 观察属性覆盖

```java
class A {
	String info = "Hello";
}
class B extends A {
	int info = 100; // 名称相同
	public void print() { // 此处为覆写
		System.out.println(super.info);
		System.out.println(info);
	}
}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.print();
	}
}
```

我们一直强调，在任何的开发之中，类型中的属性必须封装（99%），一旦封装之后，这样的覆盖是没有任何意义的。因为父类定义的私有属性不会被子类继承。

**比较：**关于super与this的区别？

| No. | 区别 | this | super |
| :---: | :---: | :---: | :---: |
| 1 | 功能 | 调用本类构造、本类方法、本类属性 | 子类调用父类构造、父类方法、父类属性 |
| 2 | 形式 | 先查找本类中是否存在有指定的调用结构，如果有则直接调用，如果没有则调用父类定义 | 不查找子类，直接调用父类操作 |
| 3 | 特殊 | 表示本类当前对象 | \- |

在以后的开发之中，对于本类或者是父类中的操作，强烈建议加上“this.”或者是“super.”。

#### 总结

1、只要发生了继承关系，那么就一定会存在有覆写的应用，覆写的应用主要以方法为主；

2、如果子类使用父类指定的方法，但是发现父类的方法实现不能够满足子类要求的时候就要使用覆写来完善子类的功能，同时保留父类的方法名称。

3、被子类覆写的方法不能拥有比父类更为严格的访问控制权限。
