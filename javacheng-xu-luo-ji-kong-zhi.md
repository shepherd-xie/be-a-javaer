## Java程序逻辑控制

程序逻辑主要分为三种逻辑结构：顺序结构、分支结构、循环结构。其中顺序结构最好理解，所有的代码都是从前向后执行的，但是需要额外提醒的是，有些时候顺序是以所在的“ {} ”为界限的。

### 分支结构

分支结构就是一种判断结构。对于分支结构有两类语法支持：if、switch。

1、if分支语句：此类语句有多种定义形式；

![](/assets/import.png)

**范例：**进行判断

```java
double score = 90;
if (score > 60.0) {
    System.out.println("及格了！");
}
```

在此样的操作里面，若果条件不满足发现就没有执行了。

**范例：**出现不满足的判断

```java
double score = 90;
if (score > 60.0) {
    System.out.println("及格了！");
} else {
    System.out.println("没及格！");
}
```

**范例：**进行多条件判断

```java
double score = 90;
if (score < 60.0) {
    System.out.println("没及格！");
} else if (score >= 60 && score <= 90) {
    System.out.println("中等成绩！");
} else if (score > 90 && score <= 100) {
    System.out.println("优秀成绩！");
} else {
    System.out.println("没有这样的成绩！");
}
```

整个if的判断都可以编写布尔表达式。但是

