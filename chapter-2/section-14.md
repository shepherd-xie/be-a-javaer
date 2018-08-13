## 继承性

---

1、清楚继承性的主要作用以及实现

2、继承性的相关限制以及使用规则。

继承性的最大特征是解决代码的重用问题。

### 继承问题的引出

下面将通过一段简短的程序来分析一下，为什么需要继承。

**范例：**要求定义两个描述人与学生的类

<table>
	<thead>
		<tr>
			<th>Person.java</th>
			<th>Student.java</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				class Person { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;private String name; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;private int age; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public void setName(String name) { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this.name = name; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public String getName() { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return name; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public void setAge(int age) { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this.age = age; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public int getAge() { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return age; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				}
			</td>
			<td>
				class Student { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;private String name; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;private int age; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;private String school;  <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public void setName(String name) { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this.name = name; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public String getName() { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return name; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public void setAge(int age) { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this.age = age; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public int getAge() { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return age; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public void setSchool(String school) { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this.school = school; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				&nbsp;&nbsp;&nbsp;&nbsp;public String getSchool() { <br />
				&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return school; <br />
				&nbsp;&nbsp;&nbsp;&nbsp;} <br />
				}
			</td>
		</tr>
	<tbody>
</table>

以上的程序里面出现了代码的重复，在自然的关系上，学生是人的一种类型，并且学生与人相比描述的更加细致，范围更小。

### 继承的实现

在Java之中如果想要实现继承使用extends关键字实现，定义时的语法结构如下；

```Java
class 子类 extends 父类 {}
```

有时，子类也会被称为派生类，父类也被称为基类或超类（super class）。

**范例：**实现继承

```java
class Person {
	private String name;
	private int age;
	public void setName(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getAge() {
		return age;
	}
}
class Student extends Person { // 继承了Person父类
}
public class MainClass {
	public static void main(String[] args) {
		Student stu = new Student();
		stu.setName("Alia"); // Person定义
		stu.setAge(16); // Person定义
		System.out.println("name:" + stu.getName() + " age:" + stu.getAge());
	}
}
```

现在Student类里面没有编写任何的方法，但是由于其继承了Person父类，所以可以直接使用Person类定义的方法。如果有需要也可以在Student类里面定义新的拓展方法。

**范例：**在Student类里面增加新的功能

```java
class Person {
	private String name;
	private int age;
	public void setName(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getAge() {
		return age;
	}
}
class Student extends Person { // 继承了Person父类
	private String school;
	public void setSchool(String school) {
		this.school = school;
	}
	public String getSchool() {
		return school;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Student stu = new Student();
		stu.setName("Alia"); // Person定义
		stu.setAge(16); // Person定义
		stu.setSchool("MIT");
		System.out.println("name:" + stu.getName() + " age:" + stu.getAge() + " school:" + stu.getSchool());
	}
}
```

实际上通过以上程序对比就可以非常清楚的发现继承的优点：

* 子类可以直接将父类的操作继续使用，属于代码重用
* 子类可以继续扩充属于自己的标准。

### 继承的限制

利用extends关键字在大部分情况下都可以不用考虑给出的继承限制，但是由于要限制用户的使用，对于继承也有一些自己的要求。

**1. Java不允许多重继承，但是允许多层继承。**

在C++语言之中具备一种概念 —— 多继承，即：一个子类可以同时继承多个父类，语法如下；

**范例：**错误的继承

```java
class A {}
class B {}
class C extends A, B {}
```

基本的要求就是希望C类可以同时继承A和B两个类，但是以上的操作是不正常的。之所以存在多继承及实际上目的只有一个：希望一个子类可以同时继承多个父类的操作。那么可以使用多层继承。

```java
class A {}
class B extends A {}
class C extends B {}
```

相当于C是B的子类，是A的孙子类。在使用多层继承的时候并没有层数的限制，不过从开发的角度认为不要超过三层的继承关系。

**2. 子类在继承父类的时候严格来讲会继承父类中的全部操作，但是对于所有的私有操作属于隐式继承，对于所有得非私有操作属于显式继承。**

**范例：**观察属性

```java
class A {
	private String msg;
	public void setMsg(String msg) {
		this.msg = msg;
	}
	public String getMsg() {
		return msg;
	}
}
class B extends A {}
public class MainClass {
	public static void main(String[] args) {
		B b = new B();
		b.setMsg("Hello");
		System.out.println(b.getMsg());
	}
}
```

在B类里面一定存在有msg属性。因为如果不存在，那么setMsg\(\)设置的内容就不可能被保存，即：getMsg\(\)是不可能进行输出的。

但是在B类里面不能够针对msg属性进行访问，因为它属于私有的，只能够间接的进行私有属性的访问。

**3. 在子类对象构造之前一定会默认调用父类的构造（默认使用无参构造），以保证父类的对象先实例化，而后再实例化子类对象。**

```java
class A {
	public A() {
		System.out.println("A、A类的构造方法！");
	}
}
class B extends A {
	public B() {
		System.out.println("B、B类的构造方法！");
	}
}
public class MainClass {
	public static void main(String[] args) {
		new B();
	}
}
```

此时并没有加入任何的操作代码，但是可以发现，在实例化子类对象前先去实例化了父类对象的构造方法。

那么此时对于子类构造而言，就相当于隐含了一个“super\(\)”。

```java
class B extends A {
	public B() {
		super(); // 父类中有无参构造是加与不加无区别
		System.out.println("B、B类的构造方法！");
	}
}
```

如果说此时父类中没有无参构造方法，那么就必须使用super\(\)明确调用父类的有参构造方法。

```java
class A {
	public A(String title) {
		System.out.println("A、A类的构造方法！");
	}
}
class B extends A {
	public B(String title) {
		super(title); // 父类中有无参构造是加与不加无区别
		System.out.println("B、B类的构造方法！");
	}
}
public class MainClass {
	public static void main(String[] args) {
		new B("Hello");
	}
}
```

通过观察可以发现“super\(\)”主要是由子类调用父类的构造方法，那么这行语句一定要放在子类构造方法的首行，这一点和this\(\)是类似的。

**疑问？**既然super\(\)和this\(\)都放在首行，如果子类构造没有编写super\(\)的话，会自动使用一个super\(\)调用父类的无参构造，那么如果写上了“this\(\)”那么是不是就表示子类无法调用无参构造了呢？

通过代码的验证：super\(\)与this\(\)不能够同时出现，无论如何编写子类构造，他都永恒有一个存在的前提：子类的对象调用前一定要先执行父类构造，为父类的对象初始化后才轮到子类对象初始化。

> 其实在Java内部，是通过隐式的组合来实现继承的。子类对象中会保存一个实例对象的引用super，该引用指向其父类。在实际的方法调用时，java会先在当前类的对象中寻找名称相同的方法，如果没有，就到super引用的父类对象中去寻找该方法，所以，若在子类中存在和父类方法签名~~和返回值类型完全~~相同的方法（overwrite）的话，java就会直接调用该对象的方法而不用去父类去寻找调用方法了。而且在子类对象中，可以直接通过super来调用父类对象中的方法。

