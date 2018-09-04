# 第 6 节 SQL限定查询

在很多时候并不需要查询所有数据行内容，此时也就可以通过WHERE子句来针对要显示的数据进行筛选，而此时可以使用的SQL语句结构如下：

```sql
【③控制要显示的数据列】SELECT [DISTINCT] * | 列名称 [别名], 列名称 [别名], ...
【①确定数据来源】 FROM 表名称 [别名]
【②确定满足条件的数据行】[WHERE 过滤条件(s)];
```

如果要想实现限定查询，那么需要掌握一系列的限定查询的符号，有以下几种：

* 关系运算符：>、<、>=、<=、<>(!=)；
* 逻辑运算符：AND、OR、NOT；
* 范围运算符：BETWEEN...AND
* 谓词范围：IN、NOT IN；
* 空判断：IS NULL、IS NOT NULL；
* 模糊查询：LIKE。

## 关系运算符

关系运算符主要是进行大小关系比较操作使用的。

**范例：** 要求查询出所有基本工资高于1500的雇员信息

```sql
SELECT *
FROM emp
WHERE sal>1500;
```

**范例：** 查询smith的完整信息

```sql
SELECT *
FROM emp
WHERE ename='SMITH';
```

在Oracle数据库之中，所有数据是需要区分大小写。

**范例：** 基础工资是5000的雇员信息

```sql
SELECT *
FROM emp
WHERE sal=5000;
```

发现“=”可以在数字上使用，也可以在字符串商是使用，在Oracle之中为了开发，所有的运算符都可以不区分数据类型，而直接使用。

**范例：** 查询职位不是销售人员的雇员编号、姓名、职位。

* 查询职位不是销售人员，指的是针对于数据行过滤；
* 雇员编号、姓名、职位，指的是针对于数据列的控制。

```sql
SELECT empno,ename,job FROM emp WHERE job!='SALESMAN';
```
```sql
SELECT empno,ename,job FROM emp WHERE job<>'SALESMAN';
```

所有的过滤都是采用条件的形式进行过滤。

## 逻辑运算符

如果说现在有多个条件要进行连接，那么就需要根据要求选择连接类型：

* 与连接：所有的判断条件都要满足；
* 或连接：若干个条件有一个满足即可。

**范例：** 查询出工资范围在1500~3000之间的雇员信息

* 条件一：sal>=1500；
* 条件二：sal<=3000；
* 关系：两个条件必须同时满足才可以显示内容，所以使用AND连接。

```sql
SELECT *
FROM emp
WHERE sal>=1500 AND sal<=3000;
```

**范例：** 查询工资大于2000或者职位是办事员的所有雇员信息

* 条件一：sal>2000；
* 条件二：job='CLERK'；
* 关系：两个条件只要满足一个即可，使用或连接。

```sql
SELECT *
FROM emp
WHERE sal>2000 OR job='CLERK';
```

在逻辑运算之中除了与和或的逻辑之外还会存在非的逻辑。

**范例：** 查询所有工资小于2000的雇员信息

```sql
SELECT *
FROM emp
WHERE sal<2000;
```

```sql
SELECT *
FROM emp
WHERE NOT sal>=2000;
```

不要写过于复杂的逻辑操作。

## 范围查询

在进行查询条件过滤的时候可以针对于某一个范围的数据进行过滤，使用BETWEEN...AND，语法：

```sql
BETWEEN 最小值（数字、日期） AND 最大值
```

在此最小值（包含）与最大值（包含）之间的内容都满足条件。

**范例：** 查询工资在1500~2000之间的雇员（包含1500、2000）

```sql
SELECT * FROM emp WHERE sal>=1500 AND sal<=2000;
```

```sql
SELECT * FROM emp WHERE sal BETWEEN 1500 AND 2000;
```

第一个查询需要匹配两个条件，而第二个查询只需要匹配一个条件。

**范例：** 查询所有在1981年雇佣的雇员

在emp表之中可以使用“hiredate”字段来描述雇佣日期，但是现在给出的是一个范围，那么就应该设置出查询的最大值与最小值，本次的两个边界值：

* 1981-01-01：'01-1月 -81'、'01-1月-1981'；
* 1981-12-31：'31-12月 -81'、'31-12月-1981'；

```sql
SELECT * FROM emp WHERE hiredate BETWEEN '01-1月 -81' AND '31-12月 -81';
```

在使用BETWEEN...AND的时候日期和数字是两个最为常用的数据类型，当然字符串也可以。

## 空判断

空在数据库上解释为不确定的内容。但是需要注意的是，如果在数字列上使用null那么绝对不表示0。对于空的判断不能够使用关系运算符。空的判断只能够使用IS NULL或IS NOT NULL（NOT IS NULL）表示。

**范例：** 查询所有领取佣金的雇员信息（佣金存在不为空）

```sql
SELECT * FROM emp WHERE comm IS NOT NULL;
```

## IN操作符

IN操作符类似于BETWEEN...AND，但是BEWTEEN...AND是给了一个大的范围，而IN给出的是一个指定的可选范围。可以说IN的使用方式与关系运算符OR类似。IN是OR运算的简化形式。

**范例：** 要求查询出雇员编号是7369、7566、7788、9999的雇员信息

* 如果不适用IN操作可以使用或操作；

```sql
SELECT * FROM emp WHERE empno=7369 OR empno=7566 OR empno=7788 OR empno=9999;
```

* 使用IN操作实现：

```sql
SELECT * FROM emp WHERE empno IN (7369,7566,7788,9999);
```

在指定值查询的过程之中，IN的操作是最简短的。

在指定的范围中查询可以使用IN，那么如果要查询不在范围中的数据就可以使用NOT IN操作。

**范例：** 要求查询出雇员编号不是7369、7566、7788、9999的雇员信息

```sql
SELECT * FROM emp WHERE empno NOT IN (7369,7566,7788,9999);
```

```sql
SELECT * FROM emp WHERE NOT empno IN (7369,7566,7788,9999);
```

_**注意：**关于NOT IN与NULL的问题_

在使用NOT IN进行范围判断是时候，如果范围里面包含有NULL，那么不会有任何的结果返回。

**范例：** 使用IN操作中包含有NULL —— 没有任何影响

```sql
SELECT * FROM emp WHERE NOT empno IN (7369,7566,7788,NULL);
```

**范例：** 使用NOT IN操作中包含有NULL

```sql
SELECT * FROM emp WHERE NOT empno NOT IN (7369,7566,7788,NULL);
```

实际上使用WHERE最大的用处在于控制显示的数据行，在简单一点：别显示全部数据行（如果要显示全部的数据行，这个过程会消耗大量的资源，甚至导致服务器宕机）。使用NOT IN的目的是查询部分数据行，但是如果有了NULL（某些数据永远不可能有NULL），就变成了查询全部。

## 模糊查询：LIKE

可以在数据库之中执行数据的模糊查询，在使用LIKE的时候可以使用两个通配符：

* “_”：匹配任意的一位字符；
* “%”：匹配任意长度字符；

**范例：** 查询姓名是以字母A开头的雇员信息

```sql
SELECT * FROM emp WHERE ename LIKE 'A%';
```

**范例：** 查询姓名的第二个字母是A的雇员信息

```sql
SELECT * FROM emp WHERE ename LIKE '_A%';
```

**范例：** 查询姓名中包含字母A的雇员信息

```sql
SELECT * FROM emp WHERE ename LIKE '%A%';
```

**注意：**关于LIKE的两点说明

说明一：LIKE可以应用在各种数据类型上，不一定非要是字符串；

```sql
SELECT * FROM emp WHEREE sal LIKE '%9%';
```

说明二：在使用LIKE模糊查询的时候，如果不设置查询关键字，那么表示查询全部；

```sql
SELECT * FROM emp WHERE ename LIKE '%%';
```

虽然以上的代码可以查询全部数据，但是极度耗费系统性能。

## 总结

1、WHERE子句一般都卸载FROM子句之后，但是是紧跟着FROM子句之后执行的；

2、WHERE子句控制显示的数据行的操作，而SELECT子句控制着显示数据列的操作。

* SELECT子句要落后于WHERE子句执行，所以在SELECT子句之中定义的别名，无法在WHERE子句里面使用；

3、使用NOT IN查询是，查询范围里面不允许出现NULL，否则不会有任何查询结果；

4、使用LIKE实现模糊查询的时候，如果不设置关键字（'%%'）表示查询全部。
