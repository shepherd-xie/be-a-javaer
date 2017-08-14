## 链表的定义与使用

---

1、本次的操作属于引用部分的加强应用，所以在此部分里面有两点需要依赖：

* 依赖于引用传递问题
* this表示当前对象

2、链表的实现的基本模式；

3、开发并且使用可用链表。

### 链表的基本形式

链表是一种最为简单的数据结构，它的主要目的是依靠引用关系来实现多个数据的保存，那么下面假设现在要保存的数据是字符串。

![](/images/chapter-2/section-13/1.png)

**范例：**定义一个Node类

* 假设本次保存的数据是String型数据，同时拥有下一个引用；

```java
// 每一个链表实际上就是由多个节点所组成的
class Node { // 定义一个节点
    private String data; // 要保存的数据
    private Node next; // 要保存的下一个节点
    // 每一个Node类对象都必须保存有相应的数据
    public Node(String data) { // 必须有数据才有Node
        this.data = data;
    }
    public void setNext(Node next) {
        this.next = next;
    }
    public Node getNext() {
        return this.next;
    }
    public String getData() {
        return this.data;
    }
}
```

以上只是一个专门负责保存节点关系的类，但是至于说怎么保存的关系，并不是由Node类进行设置。需要有其他类来进行Node的关系匹配。

**范例：**使用第一种形式设置和取出数据

```java
public class MainClass {
    public static void main(String[] args) {
        // 第一步：准备出所有数据
        Node root = new Node("火车头");
        Node n1 = new Node("车厢A");
        Node n2 = new Node("车厢B");
        root.setNext(n1);
        n1.setNext(n2);
        // 第二步：取出所有数据 
        Node currentNode = root; // 当前从根节点开始读取
        while (currentNode != null) { // 当前节点存在数据
            System.out.println(currentNode.getData());
            // 将下一个节点设置为当前节点
            currentNode = currentNode.getNext();
        }
    }
}
```

实际上以上的操作使用循环并不方便，最好的做法还是应该使用递归操作完成。

**范例：**使用第二种方式设置和取出数据

```java
public class MainClass {
    public static void main(String[] args) {
        // 第一步：准备出所有数据
        Node root = new Node("火车头");
        Node n1 = new Node("车厢A");
        Node n2 = new Node("车厢B");
        root.setNext(n1);
        n1.setNext(n2);
        print(root);
    }
    public static void print(Node current) {
        if (current == null) { // 递归结束
            return ; // 结束方法
        }
        System.out.println(current.getData());
        print(current.getNext());
    }
}
```

对于所有的节点操作由于其并不知道具体的循环次数，所以只能够使用while循环，但是在节点操作中递归的操作要比直接使用while循环代码更加的直观。

**疑问？**整个过程实际上就是设置数据和取出数据的过程。为什么需要Node？

由于数据本身不具备先后的关系，所以使用Node类来封装数据，同时利用Node类来指向下一个节点。

### 链表的基本雏形

通过分析发现：

* 用户在操作的过程之中完全没有必要去关心Node类是否存在；
* 所有的节点的引用关系不应该由用户处理，应该有一个专门的工具类来进行处理

下面需要定义一个类，来帮助客户端去隐藏所有的链表中给出的细节操作。

![](/images/chapter-2/section-13/2.png)

**范例：**链表的基本形式

```java
// 每一个链表实际上就是由多个节点所组成的
class Node { // 定义一个节点
    private String data; // 要保存的数据
    private Node next; // 要保存的下一个节点
    // 每一个Node类对象都必须保存有相应的数据
    public Node(String data) { // 必须有数据才有Node
        this.data = data;
    }
    public void setNext(Node next) {
        this.next = next;
    }
    public Node getNext() {
        return this.next;
    }
    public String getData() {
        return this.data;
    }
    // 实现节点的添加
    // 第一次调用（Link）：this = Link.root
    // 第二次调用（Node）：this = Link.root.next
    // 第三次调用（Node）：this = Link.root.next.next
    public void addNode(Node newNode) {
        if (this.next == null) { // 当前节点的下一个为空
            this.next = newNode; // 保存新节点
        } else { // 当前节点之后还存在有节点
            // 当前节点的下一个节点继续保存
            this.next.addNode(newNode);
        }
    }
    // 第一次调用（Link）：this = Link.root
    // 第二次调用（Node）：this = Link.root.next
    // 第三次调用（Node）：this = Link.root.next.next
    public void printNode() {
        System.out.println(this.data);
        if (this.next != null) {
            this.next.printNode();
        }
    }
}
// 需要进行Node类对象的关系处理
class Link { // 负责数据的设置和取出
    private Node root; // 根节点
    public void add(String data) { // 增加数据
        // 为了可以设置数据的先后关系，所以讲data包装在一个Node类对象
        Node newNode = new Node(data);
        // 保存当前数据的时候，现在还没有根节点
        if (this.root == null) { // 一个链表只有一个根节点
            this.root = newNode; // 将新的节点设置为根节点
        } else { // 根节点已经存在
            // 随后增加的元素应由节点来确定其存放的位置
            // 从root节点之后找到合适的位置
            this.root.addNode(newNode);
        }
    }
    public void print() { // 输出数据
        if (this.root != null) { // 存在根节点
            this.root.printNode(); // 交给Node类输出
        }
    }
}
public class MainClass {
    public static void main(String[] args) {
        Link link = new Link(); // 由这个类负责所有的数据操作
        link.add("Hello"); // 存放数据
        link.add("World");
        link.add("haha");
        link.add("nihao");
        link.print(); // 展示数据
    }
}
```

通过以上的代码实际上就可以发现链表的基本操作特点：

* 客户端代码不用去关注具体的Node以及引用关系的细节，只关注于提供的Link类中支持的方法；
* Link类的主要功能是控制Node类对象的产生和根节点；
* Node类主要负责数据的保存以及引用关系的分配。

### 开发可用链表

指的是可以使用链表实现数据的增加、修改、删除、查询操作。

### 程序基本结构

在开发具体的可用链表之前，首先必须明确一个道理：Node类负责所有的节点数据的保存以及节点关系的匹配。所以Node类不可能单独的去使用，而以上的实现里面Node是可以单独使用的，外部可以绕过Link类直接操作Node类，这种操作明显是没有任何意义的。所以下面必须修改设计结构，让Node类只能够被Link类使用。

这个时候使用内部类是一个最好的选择。内部类可以使用private定义，这样一个内部类只能够被一个外部类使用，并且，内部类可以方便的与外部类之间进行私有属性的直接访问。

**范例：**链表的开发结构

```java
class Link { // 链表类，外部能够看见的只有这一个类
    // 让其为Link类服务
    private class Node { // 定义的节点类
        private String data;
        private Node next;
        public Node(String data) {
            this.data = data;
        }
    }
    // ========================= 以上为内部类 =========================
    private Node root; // 需要根节点
}
```

随后进行的就是代码的填充，以及功能的完善。

### 数据增加：public void add\(数据类型 变量\)

如果要进行新数据的增加，则应该由Link类负责节点对象的产生，并且由Link类维护根节点，所有的节点关系匹配交给Node类处理。

```java
class Link { // 链表类，外部能够看见的只有这一个类
    // 让其为Link类服务
    private class Node { // 定义的节点类
        private String data;
        private Node next;
        public Node(String data) {
            this.data = data;
        }
        public void addNode(Node newNode) {
            if (this.next == null) { // 当前的下一个节点为空
                this.next = newNode;
            } else { // 向后继续保存
                this.next.addNode(newNode);
            }
        }
    }
    // ========================= 以上为内部类 =========================
    private Node root; // 需要根节点
    public void add(String data) { // 假设不允许有null
        if (data == null) {
            return ;
        }
        Node newNode = new Node(data); // 要保存的数据
        if (this.root == null) { // 当前没有根节点
            this.root = newNode; // 保存根节点
        } else { // 根节点存在，其他节点交给Node类处理
            this.root.addNode(newNode);
        }
    }
}
public class MainClass {
    public static void main(String[] args) {
        Link all = new Link();
        all.add("Hello");
        all.add("World");
        all.add(null);
    }
}
```

此时使用了一个不许为空的判断。但并不是所有的链表都不许为空。

### 取得保存元素个数：public int size\(\)

既然每一个链表对象都只有一个root根元素，那么每一个链表就有自己的长度，可以直接在Link类里面设置一个count属性，每一次数据添加完成之后，可以进行个数的自增。

**范例：**修改Link类

* 增加count属性

```java
private int count = 0; // 保存元素的个数
```

* 在add\(\)方法里增加数据的统计操作

```java
public void add(String data) { // 假设不允许有null
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
```

* 随后为Link类增加一个新的方法：size\(\)

```java
public int size() { // 取得保存的数据量
    return this.count;
}
```

本程序之中null不会被保存。

### 判断空链表：public boolean isEmpty\(\)

空链表判断实际上可以通过两种方式完成：

* 第一个：判断root有对象（是否为null）；
* 第二个：判断保存的数据量（count）。

```java
public boolean isEmpty() {
    return this.count == 0;
}
```

本次是链表组成内部的刨铣，所以一些简单的代码一定要清楚实现原理。

### 数据查询：public boolean contains\(数据类型 变量\)

在链表中一定会保存有多个数据，那么基本的判断数据是否存在的方式，以：String为例。循环链表中的内容，并且与要查询的数据进行匹配（equals\(\)），如果查到了返回true，否则返回false。

**范例：**修改Link类

```java
public boolean contains(String data) {
    // 没有要查询的数据，也不存在根节点
    if (data == null || this.root == null) {
        return false; // 没有查询结果
    }
    return this.root.containsNode(data);
}
```

从根元素开始查询数据是否存在。

**范例：**在Node类增加方法

```java
// 第一次调用（Link）：this = Link.root
// 第二次调用（Node）：this = Link.root.next
// 第三次调用（Node）：this = Link.root.next.next
public boolean containsNode(String data) {
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
```

**范例：**定义测试程序

```java
public class MainClass {
    public static void main(String[] args) {
        Link all = new Link();
        all.add("Hello");
        all.add("World");
        all.add(null);
        System.out.println(all.contains("Hello"));
        System.out.println(all.contains("heihei"));
    }
}
```

本次使用的是String型数据，所以判断数据的时候使用的是equals\(\)方法，可是如果说现在要传递的是一个自定义对象呢？则需要定义一个对象比较的方法（暂时将方法名称定义为compare\(\)）。

### 根据索引取得数据：public 数据类型 get\(int index\)

通过以上的代码测试可以发现，链表里面保存了多个String类的对象，在程序里面只有数组可以保存多个对象，使用的链表与数组相比较的话，优势就是没有长度限制，所以链表严格意义上来讲就是一个动态对象数组，那么既然链表属于动态对象数组，那么也应该具备像数组那么可以根据索引取得元素的功能。

由于是动态对象数组，所以数组中的每一个元素的索引的内容都一定是动态生成的。

#### 总结

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public void add\(数据类型 变量\) | 普通 | 向链表之中增加新的数据 |
| 2 | public int size\(\) | 普通 | 取得链表中保存的元素的个数 |
| 3 | public boolean isEmpty\(\) | 普通 | 判断是否是空链表（size\(\) == 0） |
| 4 | public boolean contains\(数据类型 变量\) | 普通 | 判断某一个数据是否存在 |
| 5 | public 数据类型 get\(int index\) | 普通 | 根据索引取得数据 |
| 6 |  | 普通 |  |
| 7 |  | 普通 |  |
| 8 |  | 普通 |  |
| 9 |  | 普通 |  |



