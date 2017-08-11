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

