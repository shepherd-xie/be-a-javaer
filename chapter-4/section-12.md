# 第 12 节 子查询

在之前编写的所有查询严格来讲都不算是复杂查询，但是如果将所有查询可以以内嵌的形式处理的话就变为了子查询，而且在所有的开发之中子查询是一定要掌握的内容，而且熟练度要高。

所谓的子查询指的是在一个完整查询语句里面嵌入N个内部查询得来的查询结果，子查询本身没有固定的语法，如果非要整合，那么可以给出如下参考：
```
⑤SELECT [DISTINCT] 分组字段 | 统计函数 | (
    ⑤SELECT [DISTINCT] 分组字段 | 统计函数 
    ①FROM 数据表1 [别名], 数据表2 [别名], ...
    ②[WHERE 过滤条件(s)]
    ③[GROUP BY 分组字段, 分组字段, ...]
    ④[HAVING 分组后过滤]
    ⑥[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
)
①FROM 数据表1 [别名], 数据表2 [别名], ... (
    ⑤SELECT [DISTINCT] 分组字段 | 统计函数 
    ①FROM 数据表1 [别名], 数据表2 [别名], ...
    ②[WHERE 过滤条件(s)]
    ③[GROUP BY 分组字段, 分组字段, ...]
    ④[HAVING 分组后过滤]
    ⑥[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
)
②[WHERE 过滤条件(s) (
    ⑤SELECT [DISTINCT] 分组字段 | 统计函数 
    ①FROM 数据表1 [别名], 数据表2 [别名], ...
    ②[WHERE 过滤条件(s)]
    ③[GROUP BY 分组字段, 分组字段, ...]
    ④[HAVING 分组后过滤]
    ⑥[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
)]
③[GROUP BY 分组字段, 分组字段, ... (
    ⑤SELECT [DISTINCT] 分组字段 | 统计函数 
    ①FROM 数据表1 [别名], 数据表2 [别名], ...
    ②[WHERE 过滤条件(s)]
    ③[GROUP BY 分组字段, 分组字段, ...]
    ④[HAVING 分组后过滤]
    ⑥[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
)]
④[HAVING 分组后过滤 (
    ⑤SELECT [DISTINCT] 分组字段 | 统计函数 
    ①FROM 数据表1 [别名], 数据表2 [别名], ...
    ②[WHERE 过滤条件(s)]
    ③[GROUP BY 分组字段, 分组字段, ...]
    ④[HAVING 分组后过滤]
    ⑥[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
)]
⑥[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
```

对于子查询而言，在那个子句之中出现并没有一个明确的结论。

* _**WHERE子句：**_当子查询返回单行单列、多行单列、单行多列；
* HAVING子句：子查询返回单行单列并且需要进行统计操作的时候；
* _**FROM子句：**_子查询返回多行多列数据可以在FROM子句中出现，按照表的形式处理；
* SELECT子句：子查询返回单行单列。

之所以会提出子查询的概念主要是为了解决查询的性能问题。

## 在WHERE子句中使用子查询

WHERE子句的主要作用：限制数据表中数据行的显示，所以一般都是做数据筛选使用的。

### 1. 子查询返回单行单列

**范例：** 查询出公司工资最低的雇员信息
* **第一步：**一定要找到公司的最低工资，这个最低工资一定是统计的结果，使用MIN()函数统计；
```sql
SELECT MIN(sal) FROM emp;
```
* **第二步：**以上的查询得到的是单行单列，并且根据现在的环境肯性是在WHERE子句中使用；
```sql
SELECT * FROM emp 
WHERE sal=(SELECT MIN(sal) FROM emp);
```

**范例：** 查询出高于公司平均工资的所有雇员信息
* **第一步：**需要统计出公司的平均工资
```sql
SELECT AVG(sal) FROM emp;
```
* **第二步：**现在返回单行单列的结果，在WHERE子句中使用
```sql
SELECT * FROM emp 
WHERE sal>(SELECT AVG(sal) FROM emp);
```

### 2. 子查询返回单行多列

所谓的单行多列指的是要求同时满足两个列的判断。例如：在之前编写的形式：sal>1000，是判断了一个列，那么如果要是想同时判断两个列，则可以使用“()”声明。

**范例：** 查询出与scott工作相同，工资相同的雇员信息
* **第一步：**查询出scott的工作和工资
```sql
SELECT job,sal FROM emp WHERE ename='SCOTT';
```
* **第二步：**由于返回单行杜烈，只能够在WHERE子句之中使用
```sql
SELECT * FROM emp
WHERE (job,sal)=(SELECT job,sal FROM emp WHERE ename='SCOTT')
    AND ename<>'SCOTT';
```

### 3. 子查询返回多行单列

一旦子查询返回多行单列的数据，实际上所返回的内容就属于一个数据的集合范围，那么杜宇范围的查询在子查询之中定义有三种操作形式：IN、ANY、ALL。
* IN操作：这一点与最初讲解的IN是完全相同的。
  * 使用IN处理：
```sql
SELECT * FROM emp
WHERE sal IN(
  SELECT sal FROM emp WHERE job='MANAGER');
```
  * NOT IN处理：不在范围之中；
```sql
SELECT * FROM emp
WHERE sal NOT IN(
  SELECT sal FROM emp WHERE job='MANAGER');
```
  * 如果使用NOT IN并且子查询中返回的结果包含有null，则不会有任何的结果返回
```sql
SELECT * FROM emp
WHERE empno NOT IN (
    SELECT mgr FROM emp);
```
* ANY操作：满足任一条件。ANY操作一共有三种使用形式：
  * `=ANY`：与任一值相等
```sql
SELECT * FROM emp
WHERE sal=ANY(
    SELECT sal FROM emp WHERE job='MANAGER');
```
  * `>ANY`：比任一值大
```sql
SELECT * FROM emp
WHERE sal>ANY(
    SELECT sal FROM emp WHERE job='MANAGER');
```
  * `<ANY`：比任一值小
```sql
SELECT * FROM emp
WHERE sal<ANY(
    SELECT sal FROM emp WHERE job='MANAGER');
```
* ALL操作：满足所有条件。有两种使用形式：
  * `>ALL`：比所有值大
```sql
SELECT * FROM emp
WHERE sal>ALL(
    SELECT sal FROM emp WHERE job='MANAGER');
```
  * `<ALL`：比所有值小
```sql
SELECT * FROM emp
WHERE sal<ALL(
    SELECT sal FROM emp WHERE job='MANAGER');
```

以上几种操作的选择，视具体要求而定。

### 在HAVING中使用子查询

如果要在HAVING中使用子查询一般而言都表示子查询返回的是单行单列，并且需要使用到分组统计的时候。

**范例：** 查询出所有高于公司平均工资的职位名称、职位人数、平均工资。
* **第一步：**需要知道公司的平均工资；
```sql
SELECT AVG(sal) FROM emp;
```
* **第二步：**针对职位进行分组统计；
```sql
SELECT job,COUNT(empno),AVG(sal)
FROM emp
GROUP BY job;
```
* **第三步：**将全公司的平均工资的就算查询设置为子查询，统计出最终的数据；
```sql
SELECT job,COUNT(empno),AVG(sal)
FROM emp
GROUP BY job
HAVING AVG(sal)>(
    SELECT AVG(sal) FROM emp);
```

### FROM子句使用子查询

FROM子句在开发之中其最主要的作用是确定要使用的数据表，实际上所谓的数据表值得就是一个行列的集合。也就是说，如果你的子查询返回的是多行多列的数据，就需要考虑将其直接放到FROM子句之中。

**范例：** 查询出每个部门的编号、名称、位置、部门人数、平均工资
* 在之前的多表查询结合分组统计的时候实现过此类的操作
```sql
SELECT d.deptno,d.dname,d.loc,COUNT(e.empno),AVG(e.sal)
FROM dept d,emp e
WHERE d.deptno=e.deptno(+)
GROUP BY d.deptno,d.dname,d.loc;
```
* 现在除了以上的做法之外，也可以更换另外一种形式实现：子查询。
  * 按照部门编号分组，统计出每个部门编号对应的人数、平均工资；
```sql
SELECT deptno,COUNT(empno),AVG(sal)
FROM emp
GROUP BY deptno;
```
  * 以上的查询返回的是一个多行多列的数据集合，即可以将查询当做临时表
```sql
SELECT d.deptno,d.dname,d.loc,temp.count,temp.avg
FROM dept d,(
    SELECT deptno,COUNT(empno) count,AVG(sal) avg
    FROM emp
    GROUP BY deptno) temp
WHERE d.deptno=temp.deptno(+);
```

**思考：**对于同样的一种查询操作，现在有了两种实现方案，那么到低有什么区别？
此时为了扩大两种方案的差异，将数据扩大`100`倍，即：`emp:1400`条记录，`dept:400`条记录。
* 多字段分组：由于在FROM子句之中存在有多张数据表，所以其操作的数据量：
  * `emp:1400*dept:400=560000`行记录。
* 子查询：由于其采用两个查询完成：
  * FROM子查询：会有`emp:1400`行记录参与到统计，最多返回`400`行记录；
  * 外部多表查询：`dept:400*temp:400=160000`行记录；
  * 总共参与的计算量：`160000+1400=161400`行记录；
很明显，采用子查询编写的性能一定会更高。所以子查询的出现可以有效的解决多表查询的性能问题。

### EXISTS运算

EXISTS运算是一种根据子查询是否有数据的形式来判断条件是否成立的运算符。

**范例：** 子查询返回数据则表示条件成立
```sql
SELECT * FROM emp
WHERE EXISTS(
    SELECT dname FROM dept);
```
**范例：** 子查询没有返回数据
```sql
SELECT * FROM emp
WHERE EXISTS(
    SELECT dname FROM dept WHERE 1=2);
```

EXISTS最大的特征只是简单的判断是否有数据返回，而并不关心返回的具体数据是什么。

**面试题：**请解释IN与EXISTS的区别？
* IN需要明确的进行数据的判断，也就是说子查询之中返回的数据内容要参与运算；
* EXISTS不需要参考具体的返回内容，其只是依靠是否有数据返回来判断条件是否成立。
