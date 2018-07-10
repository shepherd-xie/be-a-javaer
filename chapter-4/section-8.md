# 第 8 节 综合练习：基础查询

1、选择部门30中的所有员工

```sql
SELECT * FROM emp WHERE deptno=30;
```

2、列出所有办事员（CLERK）的姓名，编号和部门编号

```sql
SELECT ename, empno, deptno FROM emp WHERE job='CLERK';
```

3、找出佣金高于薪金60%的员工

```sql
SELECT * FROM emp WHERE comm>sal*0.6;
```

4、找出部门10中所有经理（MANAGER）和部门20中所有办事员（CLERK）的详细资料

* 第一组条件：部门10中所有经理，deptno=10 AND job='MANAGER'；
* 第二组条件：部门20中所有办事员，deptno=20 AND job='CLERK'；

```sql
SELECT * FROM emp 
WHERE (deptno=10 AND job='MANAGER') OR (deptno=20 AND job='CLERK');
```

5、找出部门10中所有经理（MANAGER），部门20中所有办事员（CLERK），既不是经理又不是办事员但其薪金大于等于2000的所有员工的详细资料

* 第一组条件：部门10中所有经理，deptno=10 AND job='MANAGER'；
* 第二组条件：部门20中所有办事员，deptno=20 AND job='CLERK'；
* 第三组条件：既不是经理又不是办事员但其薪金大于等于2000，job NOT IN ('MANAGER','CLERK') AND sal>=2000；

```sql
SELECT * FROM emp 
WHERE (deptno=10 AND job='MANAGER') OR (deptno=20 AND job='CLERK') OR (job NOT IN ('MANAGER','CLERK') AND sal>=2000);
```

6、找出收取佣金的员工的不同工作

* 工作一定会有重复，要消除重复

```sql
SELECT DISTINCT job FROM emp WHERE comm IS NOT NULL;
```

7、找出不收取佣金或收取佣金低于100的员工

```sql
SELECT * FROM emp WHERE comm IS NULL OR comm <100;
```

8、显示不带有“R”的员工姓名

```sql
SELECT * FROM emp WHERE ename NOT LIKE '%R%';
```

9、显示姓名包含字母“A”的所有员工的姓名，显示的结果按照基本工资由高到低、雇佣年限由早到晚、职位排序

```sql
SELECT ename FROM emp WHERE ename LIKE '%A%' ORDER BY sal DESC, hiredate, job;
```