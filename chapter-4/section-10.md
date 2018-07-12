# 第 10 节 多表查询

在之前进行查询语句编写的时候发现FROM子句之后都只是存在有一张数据表，如果现在有可能你需要的查询的数据来自多张数据表，那么此时就需要通过多表查询来解决当前的实际问题。即：FROM后可以设置多张表，多表查询的参考语法：

```sql
③SELECT [DISTINCT] * | 列名称 [别名], 列名称 [别名], ...
①FROM 数据表1 [别名], 数据表2 [别名], ...
②[WHERE 过滤条件(s)]
④[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
```

一旦程序之中出现多表查询随之而来的也会出现许多的问题，所以本次来分析多表查询的相关概念。结论：在实际的开发之中多表查询慎用（根据数据量决定）。

## 多表查询的基本概念

在进行多表查询的处理操作之前，先进行一个验证，本次准备将emp表与dept表进行多表查询关联，在这之前首先分别确认一下两张数据表中的数据量：

**范例：**查询dept表的数据量（4条数据）

```sql
SELECT COUNT(*) FROM dept;
```

**范例：**查询emp表的数据量（14条）

```sql
SELECT COUNT(*) FROM emp;
```

**范例：**直接实现emp与dept表的多表查询处理

```sql
SELECT * 
FROM emp, dept;
```

多表查询的本质就在于FROM子句之后可以编写多张表的名称。但是通过执行结果发现此时返回有56行的记录，所返回的54行记录=emp表14行记录*dept表4行记录。现在的情况是程序出现了一个“积”的结果，这样的积在数据库之中称为“笛卡尔积”。但是现在并不需要这么多乘积的结果出现，所以必须要想办法消除（笛卡尔积是永远存在的，你所关联的数据表越多，笛卡尔积越大，唯一可以消除的只是显示的信息）。

一般而言，如果要进行多表查询，往往都会有一些关联的字段或者是关联的条件。本次使用的dept和emp两张数据表之间是存在有对应的关系，雇员表中保存有一个部门编号。

而在正常情况下，关联的字段的名称都是相同的，也即是说此时如果要想消除，则在进行重名字段引用的时候最好前面加上一些标记，例如：表名称、别名。

**范例：**消除显示的笛卡尔积

```sql
SELECT * 
FROM emp, dept 
WHERE emp.deptno=dept.deptno;
```

这个时候只显示了14行记录，因为数据是以emp为主的插叙，此时证明消除了显示的笛卡尔积。一般在要进行关联操作的时候，建议使用别名，所以最合适的做法：

```sql
SELECT * 
FROM emp e, dept d 
WHERE e.deptno=d.deptno;
```

虽然以上已经成功的消除了笛卡尔积，但是需要明确的是：**_笛卡尔积永远存在_**。下面换一个用户表做观察。在Oracle数据库里面提供有一个sh的用户，这个用户下面有一些数据表的信息量是很大的。

1. 切换到sh用户：CONN sh/sh;
2. 选择两张数据表：
  * SALES表数据量：SELECT COUNT(*) FROM sales;
  * COSTS表数据量：SELECT COUNT(*) FROM costs;
3. 两张表中都存在有prod_id字段，现在就将这两张表关联在一起统计个数：

```sql
SELECT COUNT(*) FROM sales s, costs c 
WHERE s.prod_id=c.prod_id;
```

发现此时执行的速度非常慢，因为笛卡尔积的内容太大，所以多表查询当你关联的数据表越多，笛卡尔积越大，就导致你的执行速度变慢，所以多表查询慎用。

## 多表查询使用分析

多表查询前提：这些关联的数据表之间存在有关系（字段、条件）。

**范例：**查询没有雇员的编号、姓名、职位、工资、部门名称

* 确定要使用的数据表：
  * emp表：雇员的编号、姓名、职位、工资；
  * dept表：部门名称；
* 确定已知的关联字段：两张表用一个条件，三张表用两个条件，使用AND连接；
  * emp与dept：emp.deptno=dept.deptno;

**第一步：**查询每个雇员的编号、姓名、职位、工资，只需要使用一张emp表即可：

```sql
SELECT e.empno,e.ename,e.job,e.sal 
FROM emp e;
```

**第二步：**在查询之中引入部门表，但是一旦追加了新的数据表，这个时候就必须要消除笛卡尔积，所以一定要追加一个WHERE子句，编写条件：

```sql
SELECT e.empno,e.ename,e.job,e.sal,d.dname 
FROM emp e,dept d 
WHERE e.deptno=d.deptno;
```

**范例：**查询出每个雇员的编号、姓名、工资、工资等级

* 确定要使用的数据表：
  * emp表：雇员的编号、姓名、工资；
  * salgrade表：等级；
* 确定已知的关联字段：
  * 工资和工资范围：emp.sal BETWEEN salgrade.losal AND salgrade.hisal;

**第一步：**查询出每个雇员的编号、姓名、工资

```sql
SELECT e.empno,e.ename,e.sal 
FROM emp e;
```

**第二步：**追加工资等级表的信息

```sql
SELECT e.empno,e.ename,e.sal,s.grade 
FROM emp e,salgrade s 
WHERE e.sal BETWEEN s.losal AND s.hisal ;
```

**范例：**查询出每个雇员编号、姓名、职位、工资、工资等级、部门名称、部门位置

* 确定要使用的数据表：
  * emp表：雇员编号、姓名、职位、工资；
  * salgrade表：工资等级；
  * dept表：部门名称、位置；
* 确定已知的关联条件：同时满足，使用AND连接
  * 雇员和工资等级：emp.sal BETWEEN salgrade.losal AND salgrade.hisal;
  * 雇员和部门：emp.deptno=dept.deptno;

**第一步：**查询出每个雇员编号、姓名、职位、工资

```sql
SELECT e.empno,e.ename,e.job,e.sal 
FROM emp e;
```

**第二步：**引入工资等级，追加WHERE子句

```sql
SELECT e.empno,e.ename,e.job,e.sal,s.grade 
FROM emp e,salgrade s 
WHERE e.sal BETWEEN s.losal AND s.hisal;
```

**第三步：**引入部门信息

```sql
SELECT e.empno,e.ename,e.job,e.sal,s.grade,d.dname,d.loc 
FROM emp e,salgrade s,dept d
WHERE e.sal BETWEEN s.losal AND s.hisal AND e.deptno=d.deptno;
```

## 表的连接

在数据库原理之中对于多表查询实际上给出了两种表连接的概念定义：

* 内连接：等值连接、在之前进行判断的时候后都会使用一些条件（e.deptno=d.deptno），只有条件判断满足了才会显示出相应的数据信息；
* 外链接：数据表记录的全部显示，外连接分为三种：左外连接、右外连接、全外连接。

1. 如果要想进行连接的验证测试，下面需要准备出一个新的数据：
  * dept表之中一共有4行记录，其中40部门没有任何的员工，所以这是一个没有员工的部门；
  * 在emp表中追加一个没有部门的雇员，所以执行如下的数据增加操作：
```sql
INSERT INTO emp(empno,ename) VALUES(9999,'HELLO');
```
2. 使用emp表与dept表进行内容连接处理：
```sql
SELECT e.empno,e.ename,d.deptno,d.dname 
FROM emp e,dept d 
WHERE e.deptno=d.deptno ;
```
此时是一个等值判断“e.deptno=d.deptno”（内连接）凡是不满足于此条件的数据都不会显示。
3. 对于外连接分为三类（现在只能演示两类）：左外连接、右外连接：
  * 左外连接（字段=字段(+)）：左表的数据要求全部是显示；
```sql
SELECT e.empno,e.ename,d.deptno,d.dname 
FROM emp e,dept d 
WHERE e.deptno=d.deptno(+) ;
```
  * 右外连接（字段(+)=字段）：右表的数据全部显示；
```sql
SELECT e.empno,e.ename,d.deptno,d.dname 
FROM emp e,dept d 
WHERE e.deptno(+)=d.deptno ;
```

**范例：**查询每个雇员的姓名、职位、领导姓名（雇员和领导之间利用编号关联）

* 确定要使用的数据表：
  * emp表：雇员的姓名、职位，通过雇员表只能够获得领导编号；
  * emp表：通过领导编号找到领导姓名；
* 确定已知的关联字段：
  * 雇员和领导自身关联：emp.mgr=memp.empno；

**第一步：**查询每个雇员的姓名、职位

```sql
SELECT e.ename,e.job 
FROM emp e ;
```

**第二步：**查询领导的姓名，领导需要再次引入emp表，所以需要追加一个条件删除掉笛卡尔积

```sql
SELECT e.ename,e.job,m.ename  
FROM emp e,emp m 
WHERE e.mgr=m.empno;
```

**第三步：**由于之前的操作使用的是内连接，所以只要mgr的数据为null的雇员就不会显示，很明显这样的数据是有问题的，说以应该使用外连接来进行控制。

```sql
SELECT e.ename,e.job,m.ename  
FROM emp e,emp m 
WHERE e.mgr=m.empno(+) ;
```

外连接一定要根据你具体的程序查询要求来定义。

## SQL:1999连接语法

在之前已经分析过了外连接的相关操作，但是在整个实现过程之中会存在有一个问题，之前使用过的“\(+\)”符号属于Oracle自己的符号定义，如果现在更换到其它的数据库，那么有可能是不支持此类查询的，所以为了解决这样的问题，在其它数据库查询时可以采用SQL:1999语法标准进行连接实现。

```sql
SELECT table1.column,table2.column 
FROM table [CROSS JOIN table2]|
[NATURAL JOIN table2]|
[JOIN table2 USING(column_name)]|
[JOIN table2 ON(table1.column_name=table2.column_name)]|
[LEFT|RIGHT|FULL OUTER JOIN table2 ON(table1.column_name=table2.column_name)];
```

1. 交叉连接：CROSS JOIN，就是简单的将数据表关联在一起，而后会产生笛卡尔积；
```sql
SELECT * FROM emp CROSS JOIN dept;
```
2. 自然连接：NATURAL JOIN，内连接，自动找到同名的字段采用内连接的模式消除笛卡尔积；
```sql
SELECT * FROM emp NATURAL JOIN dept;
```
3. USING子句：设置要进行关联的字段；
```sql
SELECT * FROM emp JOIN dept USING(deptno);
```
4. ON子句：设置关联条件
```sql
SELECT * FROM emp e JOIN dept d ON(e.deptno=d.deptno);
```
5. 外连接：有三类外连接（LEFT OUTER JOIN）、（RIGHT OUTER JOIN）、（FULL OUTER JOIN）;
  * 左外连接：
```sql
SELECT * FROM emp e LEFT OUTER JOIN dept d ON(e.deptno=d.deptno);
```
  * 右外连接：
```sql
SELECT * FROM emp e RIGHT OUTER JOIN dept d ON(e.deptno=d.deptno);
```
  * 全外连接：
```sql
SELECT * FROM emp e FULL OUTER JOIN dept d ON(e.deptno=d.deptno);
```

这个时候没有部门的雇员和没有雇员的部门信息都会显示出来，不过一般情况下全外连接使用的比较少。

## 集合处理

数学公式中有集合的几个操作：交、并、补、差，对于查询的结果而言也可以进行集合的运算，但是很明显，如果要进行集合运算则要求查询返回的结构一定要相同。在SQL之中集合运算可以采用如下形式的语法完成：

```sql
③SELECT [DISTINCT] * | 列名称 [别名], 列名称 [别名], ... | 数据计算
①FROM 数据表1 [别名], 数据表2 [别名], ...
②[WHERE 过滤条件(s)]
④[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...]
	[UNION | UNION ALL | MINUS | INTERSECT]
	...
```

1. UNION运算：将
```sql
SELECT empno,ename,job FROM emp WHERE deptno=10
  UNION
SELECT empno,ename,job FROM emp;
```
2. UNION ALL运算：将聚合接管保存在一起显示，重复部分也显示；
```sql
SELECT empno,ename,job FROM emp WHERE deptno=10
  UNION ALL
SELECT empno,ename,job FROM emp;
```
3. MINUS运算：差集运算
```sql
SELECT empno,ename,job FROM emp
  MINUS
SELECT empno,ename,job FROM emp WHERE deptno=10;
```
4. INTERSECT运算：返回两个查询中的相同部分
```sql
SELECT empno,ename,job FROM emp
  INTERSECT
SELECT empno,ename,job FROM emp WHERE deptno=10;
```

这些运算符都是以查询结果进行处理的，也就是说可以执行多个不同的查询，而后用以上的连接符将这些结果的内容放到一起进行显示。