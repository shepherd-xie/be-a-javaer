## Java程序逻辑控制

程序逻辑主要分为三种逻辑结构：顺序结构、分支结构、循环结构。其中顺序结构最好理解，所有的代码都是从前向后执行的，但是需要额外提醒的是，有些时候顺序是以所在的“ {} ”为界限的。

### 分支结构

分支结构就是一种判断结构。对于分支结构有两类语法支持：if、switch。

1、if分支语句：此类语句有多种定义形式；

```java
if (布尔表达式) {
    程序语句;
}
```

```java
if (布尔表达式) {
    程序语句;
} else {
    程序语句;
}
```

```java
if (布尔表达式1) {
    程序语句;
} else if(布尔表达式2) {
    程序语句;
} else if(布尔表达式3) {
    程序语句;
} ...
```

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

当某段代码需要一直重复执行的时候，就可以使用循环控制结构来实现控制，对于循环结构有两种循环：while循环、for循环。

**1、while循环：分为两种形式语法**

```java
while (循环判断) {
    循环语句;
    修改循环结束条件;
}
```

```java
do {
    循环语句;
    修改循环结束条件;
} while (循环判断);
```

所有循环语句里面都必须有循环的初始化条件。每次循环的时候都要去修改这个条件，以判断循环是否结束。

**范例：**实现1~100的累加 —— 使用while循环

```java
int sum = 0;        //保存总和
int current = 1;    //循环的初始化条件
while (current <= 100) {    //循环的结束条件
    sum += current;    //累加
    current ++;
}
System.out.println(sum);
```

while循环属于先判断后执行。

**范例：**使用do...while循环

```java
int sum = 0;        //保存总和
int current = 1;    //循环的初始化条件
do {    
    sum += current;    //累加
    current ++;
} while (current <= 100);    //循环的结束条件
System.out.println(sum);
```

do...while循环指的是先执行一次，而后再进行判断，即：不管循环的条件是否满足，至少会执行一次。

**在之后的开发之中请彻底忘记掉do...while循环，根本就不会用到。因为有可能会导致极为严重的错误。**

**2、for循环，语法：**

```java
for (循环初始化条件; 循环判断; 循环条件改变) {
    循环语句;
}
```

**范例：**使用for循环实现1~100累加

```java
int sum = 0;        //保存总和
for (int current = 1; current <= 100; current ++) {
    sum += current;
}
System.out.println(sum);
```

但是有的时候for循环也会变成以下形式编写（强烈不建议这样使用）：

```java
int sum = 0;        //保存总和
int current = 1;
for (; current <= 100;) {
    sum += current;
    current ++;
}
System.out.println(sum);
```

**疑问？**现在给出了两种循环的语法，那么在开发之中使用哪种？

* 如果不知道循环次数，但是知道循环结束条件的时候就是用while循环；
* 如果已经明确了循环次数，使用for循环。

对于循环而言，往往是刚开始接触程序最麻烦的一个环节。但是对于循环本身也是可以进行嵌套的，一个循环里面嵌套着其他的循环。

**范例：**模拟看书

```java
for (int i = 1; i <= 10; i ++) {
    System.out.print("开始看第" + i + "本书");
    for (int j = 1; j <= 20; j ++) {
        System.out.print("看第" + j + "页");
    }
    System.out.println();
}
```

**范例：**九九乘法表

```java
for (int i = 1; i <= 9; i ++) {
    for (int j = 1; j <= i; j ++) {
        System.out.print(i + " * " + j + " = " + (i * j) + "\t\t");
    }
    System.out.println();
}
```



