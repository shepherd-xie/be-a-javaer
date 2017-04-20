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

整个if的判断都可以编写布尔表达式。但是switch的判断不能够使用布尔表达式，它最早的时候只能够进行整数或者是字符的判断，但是从JDK1.5开始支持了枚举判断，在JDK1.7的时候支持了String的判断。语法：

```java
switch (整数 | 字符 | 枚举 | String) {
    case 内容 : {
        内容满足时执行;
        [break;]
    }
    case 内容 : {
        内容满足时执行;
        [break;]
    }
    case 内容 : {
        内容满足时执行;
        [break;]
    } ...
    [default : {
        内容都不满足时执行;
        [break;]
    }]
}
```

在每一个case里面出现的break语句，表示的是停止case的执行，因为switch语句默认情况下会从第一个满足的case语句开始执行全部的语句代码，一直到整个switch执行完毕或者遇到了break。

**范例：**使用switch判断

```java
int ch = 2;
switch (ch) {
    case 2: {
        System.out.println("内容是2");
        break;
    }
    case 1: {
        System.out.println("内容是1");
        break;
    }
    case 3: {
        System.out.println("内容是3");
        break;
    }
    default: {
        System.out.println("没有匹配内容");
        break;
    }
}
```

switch不能判断布尔表达式，它只能够判断内容。

从JDK1.7开始switch终于出现了字符串的判断。

```java
String str = "HELLO";
switch (str) {
    case "HELLO": {
        System.out.println("内容是HELLO");
        break;
    }
    case "hello": {
        System.out.println("内容是hello");
        break;
    }
    case "world": {
        System.out.println("内容是world");
        break;
    }
    default: {
        System.out.println("没有匹配内容");
        break;
    }
}
```

程序是区分大小写的。

### 循环结构

 当某段代码需要一直重复执行的时候，就可以使用循环结构来实现控制，对于循环结构有两种循环：while循环、for循环。



