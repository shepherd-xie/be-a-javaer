## Java运算符

在之前使用过这样的语法：

```
int num = 10;
```

此类的方式成为赋值运算符，“ = ”是实现赋值运算使用的。

进行数学计算时所使用的四则运算符也属于运算符号的定义范畴。由左至右

| 优先级 | 运算符 | 类 | 结合性 |
| :---: | :---: | :---: | :---: |
| 1 | \(\) | 括号运算符 | 由左至右 |
| 1 | \[\] | 方括号运算符 | 由左至右 |
| 2 | !、+（正号）、-（负号） | 一元运算符 | 由右至左 |
| 2 | ~ | 位逻辑运算符 | 由右至左 |
| 2 | ++、-- | 递增与递减运算符 | 由右至左 |
| 3 | \*、/、% | 算术运算符 | 由左至右 |
| 4 | +、- | 算术运算符 | 由左至右 |
| 5 | &gt;&gt;、&lt;&lt; | 位左移、右移运算符 | 由左至右 |
| 6 | &gt;、&gt;=、&lt;、&lt;= | 关系运算符 | 由左至右 |
| 7 | ==、!= | 关系运算符 | 由左至右 |
| 8 | &（位运算符AND） | 位逻辑运算符 | 由左至右 |
| 9 | ^（位运算符XOR） | 位逻辑运算符 | 由左至右 |
| 10 | \|（位运算符OR） | 位逻辑运算符 | 由左至右 |
| 11 | && | 逻辑运算符 | 由左至右 |
| 12 | \|\| | 逻辑运算符 | 由左至右 |
| 13 | ?: | 三目运算符 | 由右至左 |
| 14 | = | 赋值运算符 | 由右至左 |

但是对于所有的运算符，切记不要编写过于复杂的操作。

**范例：**观察一种神奇的代码

```java
int numA = 10;
int numB = 20;
int result = numA * 2 - --numB * numA ++ + numB - numA -- + numB;
System.out.println(result);
```

以后写代码，一定要写出像“ 1 + 1 = 2”这样简单的代码，若果是真的复杂的代码，建议使用“ \(\) ”改变优先级。

### 基本运算符

在开发中常用的几类运算符：四则运算符、逻辑运算、三目运算、位运算。

对于四则运算基本的组成是：+、-、\*、/、%。

**范例：**四则运算

```java
int numA = 10;
int numB = 20;
System.out.println("加法计算" + (numA + numB));
System.out.println("减法计算" + (numA - numB));
System.out.println("乘法计算" + (numA * numB));
System.out.println("除法计算" + (numA / (double)numB));
System.out.println("加法计算" + (numA + numB));
```

**范例：**求模计算

```java
int numA = 10;
int numB = 3;
System.out.println("求模计算" + (numA % numB));
```

在运算符里面又提供了一些简化运算符：\*=、/=、+=、-=、%=。

```java
int num = 10;
num *= 2; //num = num * 2;
System.out.println(num);
```

还有一类运算符是“ ++ ”（自增）、“ -- ”（自减），它根据位置不同，执行的顺序也不同。

* ++变量、--变量：写在前面表示的是进行内容的自增1或自减1之后再使用变量进行数学计算；
* 变量++、变量--：先使用变量内容进行计算，而后在实现自增或自减的操作。

**范例；**观察自增

```java
int numA = 10;
int numB = 20;
int result = ++ numA + numB;
System.out.println("numA = " + numA);
System.out.println("result = " + result);
```

```java
int numA = 10;
int numB = 20;
int result = numA ++ + numB;
System.out.println("numA = " + numA);
System.out.println("result = " + result);
```

如果以后使用此类的操作，也只会使用“ numA ++ ”；

### 三目运算符

三目是一种赋值运算的形式，执行三目的时候可以以一个布尔表达式的结果进行赋值，基本语法结构如下：

```
数据类型 变量 = 布尔表达式 ? 满足此表达式时设置的内容 : 不满足此表达式时设置的内容;
```

**范例：**实现赋值

```java
int numA = 10;
int numB = 20;
//如果numA大于numB，返回true，则将numA的内容赋值给max
//如果numA小于numB，返回false，则将numB的内容赋值给max
int max = numA > numB ? numA : numB;
System.out.println(max);
```

如果此处不使用三目运算，则就需要编写如下形式的判断语句完成了。

```java
int numA = 10;
int numB = 20;
int max = 0;
if (numA > numB) {
    max = numA;
} else {
    max = numB;
}
System.out.println(max);
```

利用三目可以节约一些判断代码。

### 逻辑运算

对于逻辑运算主要就是：与（&、&&）、或（\|、\|\|）、非（！）三种计算。

**范例：**非就是针对布尔类型进行求反

```java
boolean flag = true;
System.out.println(!flag);
```

如果现在是多个布尔表达式想要进行连接，只能够使用与和或两个操作。

**1、与操作**

**范例：**观察普通与“ & ”

```java
if ((1 == 2) & (10 / 0 == 0)) {
    System.out.println("Hello World !");
}
```

此时使用的是一个“ & ”，发现第一个判断返回false之后第二个判断（\(10 / 0 == 0\)）继续执行，但是现在的问题是，如果前面的条件已经返回了false，后面不管有多少个true，最终的结果还是false，那么完全没有必要进行后面的判断。

**范例：**使用短路与（&&）

```java
if ((1 == 2) & (10 / 0 == 0)) {
    System.out.println("Hello World !");
}
```

此时没有出错，因为前面的条件返回了false，后面就自然不需要判断。

**2、或操作**

**范例：**观察普通或（\|）

```java
if ((1 == 1) | (10 / 0 == 0)) {
    System.out.println("Hello World !");
}
```

使用普通或的时候所有的判断条件都进行了执行，但是或运算的基本形式是：只要有一个判断返回了true，后面不管有多少个true或者是false，最终的结果永远都是true。

**范例：**观察短路或（&&）

```java
if ((1 == 1) || (10 / 0 == 0)) {
    System.out.println("Hello World !");
}
```

从此以后就是用短路与和短路或操作。

