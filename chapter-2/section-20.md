## Object类

---

1、Object类的作用；

2、Object类的常用方法；

3、Object类接收引用数据类型；

4、利用Object类修改链表。

### Object类的基本定义

Object类是所有类的父类，也就是说任何一个类在定义的手如果没有明确的继承一个父类的话，那么它就是Object类的子类，也就是说以下两种类定义的效果是完全相同的；

<table><tr><td> class Book {} </td><td> class Book extends Object {} </td><tr></table>

在整个Java里面类的继承关系一直存在（除了Object类）。

既然Object类是所有类的父类，那么就有了一个最大的好处：利用Object类可以接受全部类的对象，因为所有类都可以向上转型成Object类。

**范例：**利用Object类接收对象

```java
class Book extends Object {}
public class MainClass {
	public static void main(String[] args) {
		Object obja = new Book(); // 向上转型
		Object objb = "hello"; // 向上转型
		Book b = (Book) obja;
		String s = (String) objb;
	}
}
```

既然这样，在不确定参数类型的时候，使用Object类应该是最好的选择。

**问题：**为什么要在Object类里面定义一个无参构造方法？

Object类是所有类的父类，所有子类进行实例化时都一定要调用父类的构造方法。

从严格意义上来讲（一般不遵守），任何一个简单Java类都应该覆写Object类中的如下三个方法：

* 取得对象信息：public String toString\(\);
* 对象比较：public boolean equals\(Object obj\);
* 取得对象Hash码：public int hashCode\(\)。

### 取得对象信息：toString\(\)

如果说现在是一个String类对象，直接输出这个对象可以取得字符串的内容，但是如果是一个自定义的对象，那么就会出现一个对象编码。

**范例：**观察代码

```java
class Book extends Object {}
public class MainClass {
	public static void main(String[] args) {
		Book b = new Book();
		String s = "Hello";
		System.out.println(b);
		System.out.println(b.toString());
		System.out.println(s);
	}
}
```

发现现在如果直接输出对象与调用toString\(\)方法后输出对象的功能是完全一样的。

在进行对象输出的时候，输出操作会自动调用对象中的toString\(\)方法将对象变为字符串后再输出，而默认情况下Object类中的toString\(\)为了适应于所有对象的输出，所以只输出了对象的编码。

如果现在有需要，也可以自己根据实际情况来覆写此方法。

**范例：**覆写toString\(\)方法

```java
class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	public String toString() {
		return "title=" + this.title + " price=" + this.price;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Book b = new Book("java", 11.1);
		System.out.println(b);
	}
}
```

直接输出对象就调用了toString\(\)，等于节约了输出对象时的代码。

### 对象比较：equals\(\)

对象比较在很多开发之中都是一定要使用到的核心概念，而在之前使用了一个自定义的compare\(\)方法作为比较方法的名称，但是这个不标准，标准的做法是使用equals\(\)方法完成。

**范例：**实现对象比较

```java
class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (!(obj instanceof Book))
			return false;
		Book book = (Book) obj;
		if (!this.title.equals(book.title))
			return false;
		if (this.price != book.price)
			return false;
		return true;
	}
	public String toString() {
		return "title=" + this.title + " price=" + this.price;
	}
}
public class MainClass {
	public static void main(String[] args) {
		Book b1 = new Book("java", 11.1);
		Book b2 = new Book("java", 11.1);
		System.out.println(b1.equals(b2));
	}
}
```

### Object类可以接受一切引用数据类型

Object类是所有类的父类，所以Object类的对象可以接受所有类的对象，可是除了类的对象之外，Object还可以接受数组和接口对象。

**范例：**接受数组数据

```java
public class MainClass {
	public static void main(String[] args) {
		Object obj = new int[] {1, 2, 3};
		System.out.println(obj); // [I@7852e922
		if (obj instanceof int[]) {
			int data[] = (int[]) obj;
			for (int i = 0; i < data.length; i ++) {
				System.out.println(data[i]);
			}
		}
	}
}
```
除了数组外，接口也同样可以。

**范例：**Object类接收接口对象

```java
interface A {
	public void fun();
}
class B implements A {
	public void fun() {
		System.out.println("Hello World !");
	}
}
public class MainClass {
	public static void main(String[] args) {
		Object obj = new B();
		A a = (A) obj;
		a.fun();
	}
}
```

整个程序的参数都可以使用Object进行统一。

### 修改链表

长期一直困扰我们的是事情就是链表问题，因为在设计链表的时候一直遗留下的问题：链表不能统一数据，所以就造成了每一次使用链表的时候都要进行重复的开发，但是有了Object之后，这一切就彻底解决了，因为Object可以接受任何类型的对象，同时在链表之中需要对象比较的动能，这一功能Object也提供有容器equals\(\)方法。

**范例：**修改可用链表

```java
class Link { // 链表类，外部能够看见的只有这一个类
    // 让其为Link类服务
    private class Node { // 定义的节点类
        private Object data;
        private Node next;
        public Node(Object data) {
            this.data = data;
        }
        public void addNode(Node newNode) {
            if (this.next == null) { // 当前的下一个节点为空
                this.next = newNode;
            } else { // 向后继续保存
                this.next.addNode(newNode);
            }
        }
        // 第一次调用（Link）：this = Link.root
        // 第二次调用（Node）：this = Link.root.next
        // 第三次调用（Node）：this = Link.root.next.next
        public boolean containsNode(Object data) {
            if (data.equals(this.data)) { // 当前节点数据为要查询的数据
                return true; // 不再向后查询
            } else { // 当前节点数据不满足查询要求
                if (this.next != null ) { // 有后续节点
                    return this.next.containsNode(data);
                } else {
                    return false;
                }
            }
        }
        public Object getNode(int index) {
            // 使用当前的foot与要查询的索引进行比较
            // 随后将foot的内容自增，目的是为了进行下一步查询
            if (Link.this.foot ++ == index) { // 当前节点是要查询的索引
                return this.data; // 返回当前节点数据
            } else { // 继续向后查询
                return this.next.getNode(index);
            }
        }
        public void setNode(int index, Object data) {
            if (Link.this.foot ++ == index) {
                this.data = data; // 进行内容的修改
            } else {
                this.next.setNode(index, data);
            }
        }
        // 要传递上一个节点以及要删除的数据
        public void removeNode(Node previous, Object data) {
            if (data.equals(this.data)) { // 当前节点为要删除节点
                previous.next = this.next; // 空出当前节点
            } else { // 应该向后继续查询
                this.next.removeNode(this, data);
            }
        }
        // 第一次调用（Link）：this = Link.root
        // 第二次调用（Node）：this = Link.root.next
        public void toArrayNode() {
            Link.this.retArray[Link.this.foot ++] = this.data;
            if (this.next != null) { // 有后续元素
                this.next.toArrayNode();
            }
        }
    }
    // ========================= 以上为内部类 =========================
    private Node root; // 需要根节点
    private int count = 0; // 保存元素的个数
    private int foot = 0;
    private Object[] retArray; // 返回的数组
    public void add(Object data) { // 假设不允许有null
        if (data == null) {
            return ;
        }
        Node newNode = new Node(data); // 要保存的数据
        if (this.root == null) { // 当前没有根节点
            this.root = newNode; // 保存根节点
        } else { // 根节点存在，其他节点交给Node类处理
            this.root.addNode(newNode);
        }
        this.count ++; // 每一次保存完成后数据量加一
    }
    public int size() { // 取得保存的数据量
        return this.count;
    }
    public boolean isEmpty() {
        return this.count == 0;
    }
    public boolean contains(Object data) {
        // 没有要查询的数据，也不存在根节点
        if (data == null || this.root == null) {
            return false; // 没有查询结果
        }
        return this.root.containsNode(data);
    }
    public Object get(int index) {
        if (index >= this.count) { // 操过了查询范围（索引从0开始）
            return null; // 没有数据
        }
        this.foot = 0; // 表示从前向后查询
        return this.root.getNode(index); // 将查询过程交给Node类处理
    }
    public void set(int index, Object data) {
        if (index >= this.count) {
            return ; // 结束方法调用
        }
        this.foot = 0; // 重新设置foot属性的内容，作为索引出现
        this.root.setNode(index, data); // 交给Node类设置数据内容
    }
    public void remove(Object data) {
        if (this.contains(data)) { // 判断数据是否存在
            // 判断要删除的数据是否是根节点的数据
            if (data.equals(this.root.data)) {
                this.root = this.root.next; // 空出当前根节点
            } else { // 不是根元素
                this.root.next.removeNode(this.root, data);
            }
            this.count -- ; // 统计个数减少
        }
    }
    public Object[] toArray() {
        if (this.root == null) {
            return null;
        }
        this.foot = 0; // 需要索引控制
        this.retArray = new Object[this.count]; // 根据保存内容个开辟数组
        this.root.toArrayNode(); // 交给Node类处理
        return this.retArray;
    }
}
```

**范例：**测试

```java
public class MainClass {
	public static void main(String[] args) {
		Link all = new Link();
		all.add("A");
		all.add("B");
		all.add("C");
		all.remove("A");
		Object[] data = all.toArray();
		for (int i = 0; i < data.length; i ++) {
			System.out.println(data[i]);
		}
	}
}
```

#### 总结

1、Object类对象可以接受一切的数据类型（引用数据类型），包括数组和接口，解决了数据统一问题；

2、toString\(\)在对象输出时调用，equals\(\)在对象比较时调用。

### 综合实战：宠物商店

整合链表应用，同时进一步巩固接口的作用（标准）。

**思考：**要求以程序结构为主

实现一个宠物商店的模型，一个宠物商店可以保存多个宠物的信息（名字、年龄），可以实现宠物的上架、下架、模糊查询的功能。

![](/images/chapter-2/section-20/1.png)

现在已经有了一个可用链表，并且这两个链表里面还提供有Object统一参数。

**范例：**定义宠物的标准

```java
interface Pet { // 定义一个宠物的标准
	public String getName(); // 得到宠物的名字
	public int getAge(); // 得到宠物的年龄
}
```

宠物商店与具体的宠物没有任何的关系，它只和宠物这个接口的标准有关。

**范例：**定义宠物商店

```java
class PetShop { // 一个宠物商店要保存有多个宠物信息
	private Link pets = new Link(); // 保存的宠物信息
	public void add(Pet pet) { // 上架
		this.pets.add(pet);
	}
	public void delete(Pet pet) { // 下架
		this.pets.remove(pet);
	}
	public Link search(String keyWork) {
		Link result = new Link();
		Object[] obj = this.pets.toArray();
		for (int i = 0; i < obj.length; i ++) {
			Pet p = (Pet) obj[i];
			if (p.getName().contains(keyWork)) {
				result.add(p);
			}
		}
		return result;
	}
}
```

下面就可以根据宠物的标准定义各个子类。

**范例：**定义猫

```java
class Cat implements Pet { // 如果不实现接口无法保存宠物信息
	private String name;
	private int age;
	public Cat(String name, int age) {
		this.name = name;
		this.age = age;
	}
	public String getName() {
		return this.name;
	}
	public int getAge() {
		return this.age;
	}
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (!(obj instanceof Cat))
			return false;
		Cat other = (Cat) obj;
		if (age != other.age)
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
	public String toString() {
		return "Cat [name=" + name + ", age=" + age + "]";
	}
}
```

**范例：**定义狗

```java
class Dog implements Pet { // 如果不实现接口无法保存宠物信息
	private String name;
	private int age;
	public Dog(String name, int age) {
		this.name = name;
		this.age = age;
	}
	public String getName() {
		return this.name;
	}
	public int getAge() {
		return this.age;
	}
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (!(obj instanceof Cat))
			return false;
		Dog other = (Dog) obj;
		if (age != other.age)
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
	public String toString() {
		return "Dog [name=" + name + ", age=" + age + "]";
	}
}
```

可以发现，有了接口以后子类的形式都非常的类似，这属于接口的特点。

**范例：**测试类

```java
public class MainClass {
	public static void main(String[] args) {
		PetShop shop = new PetShop();
		shop.add(new Cat("小黑", 20));
		shop.add(new Cat("皇受", 18));
		shop.add(new Cat("吾皇", 14));
		shop.add(new Dog("二哈", 6));
		shop.add(new Dog("泰迪", 16));
		shop.add(new Dog("阿拉斯加", 18));
		shop.delete(new Dog("泰迪", 16));
		Link all = shop.search("皇");
		Object[] objs = all.toArray();
		for (int i = 0; i < objs.length; i ++) {
			System.out.println(objs[i]);
		}
	}
}
```

此时实现了现实的关系的模拟。

