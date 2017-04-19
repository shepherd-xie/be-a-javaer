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
int max = numA > numB ? numA : numB;
```



