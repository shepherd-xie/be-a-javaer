# 第 9 节 单行函数

虽然各个数据库有各个数据库自己的开发架构，但是对于所有的数据库而言，基本上对开发者只有两块重要的内容：SQL语句+单行函数。

所有的单行函数，严格来讲，程序都可以处理。

所谓的单行函数值的就是完成某一具功能的操作函数，例如：转大写、或者说进行日期格式的转换等，一般而言，单行函数的格式：“返回值 函数名称\(参数\)”。

单行函数按照类型一共分为以下几种：字符串函数、数值函数、日期函数、转换函数、通用函数。

## 字符串函数

字符串函数主要是处理字符串数据的（对于字符串的数据有可能是从列上找到的，或者是直接设置的字符串常量），包含的函数有如下几种。

|  | No. | 函数名称 | 描述 |
| :---: | :---: | :---: | :--- |
| 1 | 字符串 UPPER\(列 | \|字符串\) | 将传入的字符串变为大写字母形式 |
| 2 | 字符串 LOWER\(列 | 字符串\) | 将传入的字符串变为小写字母形式 |
| 3 | 字符串 INITCAP\(列 | 字符串\) | 首字母大写，其余变为小写 |
| 4 | 数字 LENGTH\(列 | 字符串\) | 取得执行字符串的长度 |
| 5 | 字符串 SUBSTR\(列 | 字符串, 开始索引, \[长度\]\) | 进行字符串的截取，如果没有设置长度，表示从开始索引一直截取到结尾 |
| 6 | 字符串 REPLACE\(列 | 字符串, 旧内容, 新内容\) | 将指定字符串的数据以新数据替换旧字符串 |

在Oracle里面，所有的函数如果要想进行验证，也必须编写SQL语句。为了方便用户进行一些验证或者是一些不需要查询表的操作，专门提供了一个dual的虚拟表。

1、转大写、小写操作

**范例：**观察基本操作

```sql
SELECT UPPER('hello') FROM dual;
```

**范例：**将所有的姓名转小写

```sql
SELECT LOWER(ename) FROM emp;
```

在Oracle数据库里面本身数据是区分大小写的，但是在实际的开发之中有许多的代码本身是不区分大小写的，所以用户进行信息查询的时候也往往不会考虑到大小写的问题。

**范例：**由用户输入要查询的雇员姓名，而后显示雇员的完整信息

* 在Oracle里面如果想要实现数据的输入操作，可以使用替代变量的方式完成，格式“&标记”；

```sql
SELECT * FROM emp WHERE ename=UPPER('&inputname');
```

2、首字母大写其他字母小写

**范例：**将所有的雇员姓名以首字母大写的形式保存

```sql
SELECT ename, INITCAP(ename) FROM emp;
```

3、取得字符串的长度

**范例：**基础操作

```sql
SELECT LENGTH('helloworld!!!') FROM dual;
```

**范例：**查询雇员姓名长度为5的全部雇员信息

```sql
SELECT * FROM emp WHERE LENGTH(ename)=5;
```

4、字符串截取

* 从指定位置截取到结尾：字符串 SUBSTR\(列 \| 字符串, 开始索引\);
* 截取部分内容：字符串 SUBSTR\(列 \| 字符串, 开始索引, 长度\);

**范例：**验证函数

```sql
SELECT SUBSTR('helloworld',6) FROM dual;
```

```sql
SELECT SUBSTR('helloworld',0,5) FROM dual;
```

```sql
SELECT SUBSTR('helloworld',1,5) FROM dual;
```

在程序之中所有的字符串的首字母的索引都是0，但是在Oracle里面，所有的字符串的首字母的索引都是1，如果你设置的是0，那么它也会按照1的方式来进行处理。

**范例：**要求截取每个雇员姓名的前三个字母

```sql
SELECT ename,SUBSTR(ename,1,3) FROM emp;
```

**范例：**取每个雇员姓名的后三位字母

* 如果要想取出后三位字母，那么首先必须要知道截取的开始索引。

```sql
SELECT SUBSTR(ename,LENGTH(ename)-2) FROM emp;
```

* 对于Oracle而言则可以利用其特性，将索引设置为负数，表示由后向前截取。

```sql
SELECT SUBSTR(ename,-3) FROM emp;
```

这样的设置方式只有Oracle数据库才有。

## 数值函数

数值函数主要是进行数字的处理，最为核心的数值函数一共有三个。

|  |  | No. | 函数名称 | 描述 |
| :---: | :---: | :---: | :--- | :--- |
|  | 1 | 数字 ROUND\(列 | 数字\[, 小数位\]\) | 实现数据的四舍五入，可以保留小数位 |
|  | 2 | 数字 TRUNC\(列 | 数字\[, 小数位\]\) | 实现数据的截取，即：不进位 |
| 3 | 数字 MOD\(列 | 数字， 列 | 数字\) | 求模\(计算余数\) |

**范例：**使用ROUND\(\)函数

```sql
SELECT ROUND(789.5671234) FROM dual;
```

如果没有设置小数点的保留位数，那么会直接不保留小数位进位。

**范例：**使用ROUND\(\)函数

```sql
SELECT ROUND(789.5671234,2) FROM dual;
```

如果设置了小数位的话，那么就会在指定的小数位上实现四舍五入。

**范例：**可以设置为负数

```sql
SELECT ROUND(789.5671234,-2) FROM dual;
```

如果设置为负数，那么就表示进行整数位的四舍五入。

**范例：**验证TRUNC\(\)函数

TRUNK\(\)函数与ROUND\(\)函数的使用形式上差别不大，唯一的区别在于TRUNC\(\)是不会进位的。

```sql
SELECT TRUNC(789.5671234),
    TRUNC(789.5671234,2),
    TRUNC(789.5671234,-2) FROM dual;
```

**范例：**求模函数

```sql
SELECT MOD(10,3) FROM dual;
```

## 日期函数

取得日期时间，Oracle里面专门提供了一个伪列“SYSDATE”（SYSTIMESTAMP）。

**范例：**验证伪列

```sql
SELECT SYSDATE FROM dual;
```

```sql
SELECT SYSTIMESTAMP FROM dual;
```

**范例：**进一步观察伪列

```sql
SELECT ename,job,sal,SYSDATE FROM emp;
```

SYSDATE伪列里面包含有日期时间的内容，只不过现在只显示了日期数据。

对于日期的操作有三个主要的公式：

* 日期 + 数字 = 日期（若干天之后的日期）；
* 日期 - 数字 = 日期（若干天之前的日期）；
* 日期 - 日期 = 数字（天数）

**范例：**实现日期的基本操作

```sql
SELECT SYSDATE-7,SYSDATE+120 FROM dual;
```

对于日期而言，由于每个月的天数是不相同的，所以直接进行天数加法实现月数的计算是不精确的。

**范例：**要求查询出每个雇员的编号、姓名、职位、已经被雇佣的天数

```sql
SELECT empno,ename,job,SYSDATE-hiredate FROM emp;
```

如果直接使用天数来实现年或月的计算，那么最终的结果一定是不准确的。

为了准确的进行日期操作，在Oracle里面提供有四个日期处理函数。

|  |  | No. | 函数名称 | 描述 |
| :---: | :---: | :---: | :--- | :--- |
|  | 1 | 日期 ADD\_MONTHS\(列 | 日期, 月数\) | 在指定的日期上增加若干个月之后的日期 |
| 2 | 数字 MONTHS\_BETWEEN\(列 | 日期, 列 | 日期\) | 返回两个日期之间所经历的月 |
|  | 3 | 日期 LAST\_DAY\(列 | 日期\) | 取得指定日期所在月的最后一天 |
|  | 4 | 日期 NEXT\_DAY\(列 | 日期, 星期X\) | 返回下一个指定的一周时间数对应的日期 |

**范例：**在当前日期下增加指定的月数

```sql
SELECT ADD_MONTHS(SYSDATE,4) FROM dual;
```

在进行月数增加的时候是不会限制数据的大小的。

**范例：**计算所有雇员到今天为止雇佣的月数

```sql
SELECT empno,ename,hiredate,MONTHS_BETWEEN(SYSDATE,hiredate) FROM emp;
```

**范例：**计算当前时间所在月的最后一天日期

```sql
SELECT LAST_DAY(SYSDATE) FROM dual;
```

**范例：**要求查询出所有在雇佣所在月倒数第三天雇佣的雇员信息

```sql
SELECT empno,ename,hiredate FROM emp WHERE hiredate=LAST_DAY(hiredate)-2;
```

一般而言，日期函数的操作过程是有些麻烦的，但是有一个前提：利用日期函数操作的日期是最准确的。

**范例：**验证“NEXT\_DAT\(\)”函数

```sql
SELECT NEXT_DAY(SYSDATE,'星期二') FROM dual;
```

**范例：**要求以年、月、日的方式计算出每个雇员到现在为止雇佣年限

例如：7698的雇员BLAKE，雇佣的日期是：1981-05-01，现在的日期是2018-04-11，此雇员到现在为止已经被雇佣了：36年、11月、10天。本次要求的时间精度很高，只能通过时间函数进行操作。

**第一步：**求出每一位雇员到现在为止雇佣的年份

在整个Oracle里面提供了两种方式来计算年份：

* 方式一：（日期 - 日期 = 天数） ÷ 365 = 年，这种方式无法规避闰年的问题；
* 方式二：MONTHS\_BETWEEN\(SYSDATE,hiredate\) ÷ 12；

```sql
SELECT empno,ename,hiredate,
    TRUNC(MONTHS_BETWEEN(SYSDATE,hiredate)/12) year
FROM emp;
```

**第二步：**求出雇佣的月数

在计算年的时候使用了“总月数 ÷ 12”，但是计算的结果里面发现有余数，这些余数实际上就是剩余的月数。

```sql
SELECT empno,ename,hiredate,
    TRUNC(MONTHS_BETWEEN(SYSDATE,hiredate)/12) year,
    TRUNC(MOD(MONTHS_BETWEEN(SYSDATE,hiredate),12)) month
FROM emp;
```

**第三步：**求出雇佣的天数

在Oracle之中提供的计算天数的操作只有一种形式“日期1 - 日期2 = 天数”；

* 日期1：使用SYSDATE，表示当前的日期时间；
* 日期2：ADD\_MONTHS\(hiredate,MONTHS\_BETWEEN\(SYSDATE,hiredate\)\)；
  * 规避：由于时间跨度太长，必须规避掉年和月的问题；
  * 分析：雇佣日期 + （雇佣日期到现在为止所经历的月数）

```sql
SELECT empno,ename,hiredate,
    TRUNC(MONTHS_BETWEEN(SYSDATE,hiredate)/12) year,
    TRUNC(MOD(MONTHS_BETWEEN(SYSDATE,hiredate),12)) month,
    TRUNC(SYSDATE-ADD_MONTHS(hiredate,MONTHS_BETWEEN(SYSDATE,hiredate))) day
FROM emp;
```



