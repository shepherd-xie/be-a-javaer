## 数据表与简单Java类映射

---

在之前已经给出了简单Java类一定要和数据表的结构对应，同时外键需要依靠引用来进行关联，于是，本次将针对简单Java类做一个功能上的扩充。

要求通过Java程序描述出dept-emp关系，使用字段:

* dept：deptno、dname、loc；
* emp：empno、ename、job、sal、deptno、mgr。

在dept-emp表的关系里面存在有如下的关系：

* 一个部门有多个雇员；
* 一个雇员有一个或零个领导。

**第一步：**实现基本字段的转换

```java
class Dept {
    private int deptno;
    private String dname;
    private String loc;
    public Dept(int deptno, String dname, String loc) {
        this.deptno = deptno;
        this.dname = dname;
        this.loc = loc;
    }
    public String getInfo() {
        return "deptno=" + deptno + ", dname=" + dname + ", loc=" + loc;
    }
}
class Emp {
    private int demo;
    private String ename;
    private String job;
    private double sal;
    private double comm;
    public Emp(int demo, String ename, String job, double sal, double comm) {
        this.demo = demo;
        this.ename = ename;
        this.job = job;
        this.sal = sal;
        this.comm = comm;
    }
    public String getInfo() {
        return "demo=" + demo + ", ename=" + ename + ", job=" + job + ", sal=" + sal + ", comm=" 
+ comm;
    }
}
```

**第二步：**解决外键关系

一个雇员属于一个部门，应该在雇员里面保存部门信息

* Emp类：

```java
private Dept dept;    // 表示对应的部门信息
public void setDept(Dept dept) {
    this.dept = dept;
}
public Dept getDept() {
    return this.dept;
}
```

一个部门有多个雇员

* Dept类

```java
private Emp[] emps;    // 多个雇员
public void setEmps(Emp[] emps) {
    this.emps = emps;
}
public Emp[] getEmps() {
    return this.emps;
}
```

一个雇员有一个领导

```java
private Emp mgr;    // 表示雇员对应的领导
public void setMgr(Emp mgr) {
    this.mgr = mgr;
}
public Emp getMgr() {
    return this.mgr;
}
```

所有匹配的映射关系都已经可以成功的描述出来了。

**第三步：**设置并取得数据

对于这种映射的方式一定要分两步完成，第一步根据结构设置数据，第二步根据结构取出数据。

* 设置数据

```java
public class MainClass {
    public static void main(String[] args) {
        // 第一步：设置数据
        // 1、产生各自的独立对象
        Dept dept = new Dept(10, "ACCOUNTING", "New York"); // 部门信息
        Emp ea = new Emp(7369, "SMITH", "CLERK", 800.0, 0.0); // 雇员信息
        Emp eb = new Emp(7902, "FORD", "MANAGER", 2450.0, 0.0); // 雇员信息
        Emp ec = new Emp(7839, "KING", "PRESIDENT", 5000.0, 0.0); // 雇员信息
        // 2、设置雇员和领导关系
        ea.setMgr(eb);
        eb.setMgr(ec);
        // 3、设置雇员和部门关系
        ea.setDept(dept); // 雇员与部门
        eb.setDept(dept); // 雇员与部门
        ec.setDept(dept); // 雇员与部门
        dept.setEmps(new Emp[] {ea, eb, ec});
    }
}
```

* 取出数据

根据给定结构取出数据，要求如下：

* 可以根据一个雇员查询他对应的领导信息和部门信息；
* 可以根据一个部门取出所有的雇员以及每个雇员的领导信息；

```java
System.out.println(ea.getInfo());
System.out.println("\t|-" + ea.getMgr().getInfo());
System.out.println("\t|-" + ea.getDept().getInfo());
```

```java
System.out.println(dept.getInfo());
for (int i = 0; i < dept.getEmps().length; i ++) {
    System.out.println("\t|- " + dept.getEmps()[i].getInfo());
    if (dept.getEmps()[i].getMgr() != null) {
        System.out.println("\t\t|- " + dept.getEmps()[i].getMgr().getInfo());
    }
}
```

整个代码之中都是依靠代码链进行数据的取出的。

### 题目一：一对多

* 1、要求可以通过一个城市找到它对应的省份信息；
* 2、可以通过一个省份找到所有城市的信息；

**第一步：**先写出基本字段

```java
class Province { // 类名称就是表名称
    private int pid;
    private String name;
    public Province(int pid, String name) {
        this.pid = pid;
        this.name = name;
    }
    public String getInfo() {
        return "pid=" + pid + ", name=" + name;
    }
}
class City {
    private int cid;
    private String name;
    public City(int cid, String name) {
        this.cid = cid;
        this.name = name;
    }
    public String getInfo() {
        return "cid=" + cid + ", name=" + name;
    }
}
```

**第二步：**设置关联字段\(引用\)

```java
class Province { // 类名称就是表名称
    private int pid;
    private String name;
    private City[] cities;
    public Province(int pid, String name) {
        this.pid = pid;
        this.name = name;
    }
    public void setCities(City[] cities) {
        this.cities = cities;
    }
    public City[] getCities() {
        return this.cities;
    }
    public String getInfo() {
        return "pid=" + pid + ", name=" + name;
    }
}
class City {
    private int cid;
    private String name;
    private Province province;
    public City(int cid, String name) {
        this.cid = cid;
        this.name = name;
    }
    public void setProvince(Province province) {
        this.province = province;
    }
    public Province getProvince() {
        return this.province;
    }
    public String getInfo() {
        return "cid=" + cid + ", name=" + name;
    }
}
```

**第三步：**进行代码测试

> 按照两步完成，第一步是设置数据，第二步是根据结构取出数据。

```java
public class MainClass {
    public static void main(String[] args) {
        // 第一步：设置关系数据
        // 1、先准备好各自独立的对象
        Province pro = new Province(1, "Shaan xi");
        City c1 = new City(1001, "Xi'an");
        City c2 = new City(1002, "Baoji");
        City c3 = new City(1003, "Xianyang");
        // 2、设置关系
        c1.setProvince(pro); // 一个城市属于一个省份
        c2.setProvince(pro);
        c3.setProvince(pro);
        pro.setCities(new City[] {c1, c2, c3}); // 一个省份有多个城市
        // 第二步：取出关系数据
        System.out.println(c2.getProvince().getInfo());
        for (int i = 0; i < pro.getCities().length; i ++) {
            System.out.println("\t|- " + pro.getCities()[i].getInfo());
        }
    }
}
```

### 题目二：一对多

* 1、可以通过一个类型找到它所对应的全部子类型；
* 2、可以通过一个类型找到他所对应的全部商品，以及每个商品对应的子类型；
* 3、可以通过一个子类型找到所有对应的全部商品。

**第一步：**设计基本字段

```java
class Item { // 父栏目
	private int iid;
	private String name;
	private String note;
	public Item(int iid, String name, String note) {
		this.iid = iid;
		this.name = name;
		this.note = note;
	}
	public String getInfo() {
		return "iid=" + iid + ", name=" + name + ", note=" + note;
	}
}
class Subitem { // 子栏目
	private int sid;
	private String name;
	private String note;
	public Subitem(int sid, String name, String note) {
		this.sid = sid;
		this.name = name;
		this.note = note;
	}
	public String getInfo() {
		return "sid=" + sid + ", name=" + name + ", note=" + note;
	}
}
class Product { // 商品
	private int pid;
	private String name;
	private double price;
	public Product(int pid, String name, double price) {
		this.pid = pid;
		this.name = name;
		this.price = price;
	}
	public String getInfo() {
		return "pid=" + pid + ", name=" + name + ", price=" + price;
	}
}
```

**第二步：**设置关系

```java
class Item { // 父栏目
	private int iid;
	private String name;
	private String note;
	private Subitem[] subitems;
	private Product[] products;
	public Item(int iid, String name, String note) {
		this.iid = iid;
		this.name = name;
		this.note = note;
	}
	public void setSubitems(Subitem[] subitems) {
		this.subitems = subitems;
	}
	public Subitem[] getSubitems() {
		return this.subitems;
	}
	public void setProducts(Product[] products) {
		this.products = products;
	}
	public Product[] getProducts() {
		return this.products;
	}
	public String getInfo() {
		return "iid=" + iid + ", name=" + name + ", note=" + note;
	}
}
class Subitem { // 子栏目
	private int sid;
	private String name;
	private String note;
	private Item item;
	private Product[] products;
	public Subitem(int sid, String name, String note) {
		this.sid = sid;
		this.name = name;
		this.note = note;
	}
	public void setItem(Item item) {
		this.item = item;
	}
	public Item getItem() {
		return this.item;
	}
	public void setProducts(Product[] products) {
		this.products = products;
	}
	public Product[] getProducts() {
		return this.products;
	}
	public String getInfo() {
		return "sid=" + sid + ", name=" + name + ", note=" + note;
	}
}
class Product { // 商品
	private int pid;
	private String name;
	private double price;
	private Item item;
	private Subitem subitem;
	public Product(int pid, String name, double price) {
		this.pid = pid;
		this.name = name;
		this.price = price;
	}
	public void setItem(Item item) {
		this.item = item;
	}
	public Item getItem() {
		return this.item;
	}
	public void setSubitem(Subitem subitem) {
		this.subitem = subitem;
	}
	public Subitem getSubitem() {
		return this.subitem;
	}
	public String getInfo() {
		return "pid=" + pid + ", name=" + name + ", price=" + price;
	}
}
```

**第三步：**设置关系以及数据的取出

```java
public class MainClass {
	public static void main(String[] args) {
		// 第一步：设置数据关系
		// 1、准备出单独的类对象
		Item item = new Item(1, "PC", "-");
		Subitem suba = new Subitem(1001, "CPU", "-");
		Subitem subb = new Subitem(1002, "Hard disk", "-");
		Subitem subc = new Subitem(1003, "Graphics", "-");
		Product proa = new Product(90001, "Intel CORE i7", 998);
		Product prob = new Product(90002, "Intel CORE i5", 798);
		Product proc = new Product(90003, "Seagate 1TB", 398);
		Product prod = new Product(90004, "Seagate 500MB", 358);
		Product proe = new Product(90005, "NVIDIA 1080Ti", 1080);
		Product prof = new Product(90006, "NVIDIA TITAN", 9998);
		// 2、设置对象间的引用关系
		suba.setItem(item);
		subb.setItem(item);
		subc.setItem(item);
		item.setSubitems(new Subitem[] {suba, subb, subc});
		proa.setSubitem(suba);
		proa.setItem(item);
		prob.setSubitem(suba);
		prob.setItem(item);
		proc.setSubitem(subb);
		proc.setItem(item);
		prod.setSubitem(subb);
		prod.setItem(item);
		proe.setSubitem(subc);
		proe.setItem(item);
		prof.setSubitem(subc);
		prof.setItem(item);
		suba.setProducts(new Product[] {proa, prob});
		subb.setProducts(new Product[] {proc, prod});
		subc.setProducts(new Product[] {proe, prof});
		item.setProducts(new Product[] {proa, prob, proc, prod, proe, prof});
		// 第二步：取出数据
		// 1、通过一个类型找到对应的全部子类型
		System.out.println(item.getInfo());
		for (int i = 0; i < item.getSubitems().length; i ++) {
			System.out.println("\t|- " + item.getSubitems()[i].getInfo());
		}
		System.out.println("----------------------------------------------------------");
		// 2、通过一个类型找到他所对应的全部商品，以及每个商品对应的子类型
		System.out.println(item.getInfo());
		for (int i = 0; i < item.getProducts().length; i ++) {
			System.out.println("\t|- " + item.getProducts()[i].getInfo());
			System.out.println("\t\t|- " + item.getProducts()[i].getSubitem().getInfo());
		}
		System.out.println("----------------------------------------------------------");
		// 3、通过一个子类型找到所有对应的全部商品
		System.out.println(subb.getInfo());
		for (int i = 0; i < subb.getProducts().length; i ++) {
			System.out.println("\t|- " + subb.getProducts()[i].getInfo());
		}
	}
}
```



```java



```



