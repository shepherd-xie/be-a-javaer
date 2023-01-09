# 第 5 节 SQL简单查询

---

所谓的简单查询就是查询一张数据表中所有数据行的内容。其使用的语法格式如下：

```sql
② SELECT [DISTINCT] * | 列名称 [别名], 列名称 [别名], ...
① FROM 表名称 [别名];
```

在以上的结构中，首先执行的是FROM子句，因为必须通过FROM子句确定数据的来源，而后要针对于数据的筛选操作，通过SELECT子句完成。

**范例：** 查询emp表中的全部记录

* 如果是全部记录指的就是所有的行和列的数据，简单查询是不能控制数据行的，只能够在SELECT子句里面控制列，如果是全部的查询列，那么就使用通配符“*”完成。

```sql
SELECT * FROM emp;
```

**范例：** 查询每个雇员的编号、姓名、职位、工资

* 此时不再表示查询全部的数据列，只表示查询几个固定的数据列，所以在SELECT子句之后要写上具体的列名称。

```sql
SELECT empno, ename, job, sal FROM emp;
```

所谓的简单查询就是查询全部的数据行记录，但是由SELECT子句控制列。

**范例：** 查询所有的职位信息，职位信息是job列

```sql
SELECT job FROM emp;
```

此时的确是查出了所有的job列的内容，可是里面发现有重复的数据存在，如果要想去除掉所有重复的信息，那么可以使用“DISTINCT”完成，此选项只能够出现在SELECT子句的后面。

**范例：** 消除掉重复的内容

```sql
SELECT DISTINCT job FROM emp;
```

此时的确消除掉了重复的内容，但是需要说明的是，如果查询的数据是多个列，那么只有在这多个列数据都相同的时候才可以消除。

**范例：** 观察消除的问题

```sql
SELECT DISTINCT empno, job FROM emp;
```

除了进行简单查询之外，也可以针对于查询列的返回结构进行四则运算。

**范例：** 查询每个雇员的编号、姓名、基本年薪

```sql
SELECT empno, ename, sal*12 FROM emp;
```

但是这个时候发现查询出来的结果列上有一些标记不清楚，所以为了改善显示效果，可以使用别名定义。

**范例：** 定义别名

```sql
SELECT empno, ename, sal*12 income FROM emp;
```

但是对于别名有一点说明：不建议使用中文。

**范例：** 要求显示出每个雇员的编号、姓名、基本年薪（每年可以领取15个月工资，每个月有200元的饭食补贴、100元的汽车补贴、100元的电话补贴，每年还有5个月的高温补贴200元）

```sql
SELECT empno, ename, 
(sal*15 + (200 + 100 + 100) * 12 + 200 * 5) income FROM emp;
```

如果执行的是有四则运算的部分，依然是使用先乘除后加减的形式。

在使用SELECT子句查询数据时候，除了查询列实际上也可以设置一些常量，这些常量可以直接进行输出。但是对于常量也有如下的三点说明：

* 如果常量是字符串，则要求使用“'”声明，例如：'hello'；
* 如果常量是数字，则直接编写，例如：10；
* 如果常量是日期，则按照日期风格格式编写，使用“xx日-xx月-xx年”，例如：“17-12 月-80”；

**范例：** 直接查询常量

```sql
SELECT '雇员', empno, ename, FROM emp;
```

在进行简单查询操作里面，如果有需要也可以在SELECT子句里面使用“||”连接查询结果。

**范例：** 观察连接效果

```sql
SELECT empno||ename FROM emp;
```

现在相当于将empno与ename两个列的内容合并为一个列进行显示了。

**范例：** 转换显示格式

* 效果：雇员编号：_7369_，姓名：_SMITH_，收入：_800_

```sql
SELECT '雇员编号：' || empno || '，姓名：' || ename || '，收入：' sal info 
FROM emp;
```

在SELECT子句之中出现的任何字符串都要求使用“'”声明，而别名不需要做声明，直接使用即可。

## 总结

1、简单查询是将一张数据表中所有数据行的内容都显示出来；

2、在查询语句之中先执行FROM子句确定数据来源（此时实际上是所有的行和列数据），而后利用SELECT子句可以控制要显示的数据列；

3、如果出现数据的从夫，可以使用DISTINCT来消除重复的数据行显示；

4、SELECT子句可以进行四则运算；

5、SELECT子句可以直接数据长岭内容，但是对于字符串使用“'”、数字直接编写、日期按照字符串格式；

6、“||”负责进行输出的内容链接，但是需要注意的是，一般此类的操作很少直接在查询中出现。