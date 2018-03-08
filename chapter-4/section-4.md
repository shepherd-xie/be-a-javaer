# 第 4 节 SQL简介与数据表分析

---

## SQL简介

SQL指的是结构化查询语言(Structured Query Language)。在世界上80年代的时候基本上存在有80多种数据库。这个时候有一个情况非常麻烦，每一种数据库都有自己的一套操作命令。这样一来给予开发者非常大的开发难度，因为习惯于使用A数据库的用户如果换到了B数据库，基本上就表示需要重新学习。

在70年代末的时候由IBM开发出了最早的SQK操作标准，而Oracle数据库是世界上第一个支持SQL语法的数据库，后来发展到今天，基本上所有的关系型数据库都支持SQL语法。

这样就带来了一个好处，即：如果你本身擅长于使用DB2数据库，那么就可以很轻松的上手Oracle数据库，或者是SQL Server数据库（MySQL）。

但是需要提醒的是，SQL语句语法没有那么复杂：SELECT、FROM、WHERE、GROUP BY、HAVING、ORDER BY、INSERT、UPDATE、DELETE、CREATE、DROP、ALTER、GRANT、REVOKE。

严格来讲SQL会分为三种类型：

* DML（数据操作语言，开发中使用的部分）：主要指的是数据库的查询与更新操作，查询操作是整个SQL的核心；
* DDL（数据定义语言，开发前的设计）：主要指的是数据对象的创建（表、用户），例如：CREATE、DROP、ALTER，这一部分的操作需要使用到相关的设计范式；
* DCL（数据控制语言，系统人员工作）：主要是进行权限的管理操作（需要结合用户），此部分由DBA负责；

## SCOTT用户表的结构

SCOTT用户一共有四张数据表，这四张表的表结构为以后学习过程中使用的部分。首先了解以下两个命令：

**命令一：**查询一个用户下的所有数据表

```sql
SELECT * FROM tab;
```

**命令二：**查询一个表的结构

```sql
DESC 表名称
```

**范例：**查看dept表结构

```sql
DESC dept;
```

**1、部门信息表：dept**

| No. | 列名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | DEPTNO | NUMBER(2) | 表示部门编号，最多由2位数字所组成 |
| 2 | DNAME | VARCHAR2(14) | 表示部门名称，最多由14个字符所组成 |
| 3 | LOC | VARCHAR2(13) | 部门位置 |

```sql
SQL> SELECT * FROM dept;

    DEPTNO DNAME                        LOC
---------- ---------------------------- --------------------------
        10 ACCOUNTING                   NEW YORK
        20 RESEARCH                     DALLAS
        30 SALES                        CHICAGO
        40 OPERATIONS                   BOSTON
```

**2、雇员信息表：emp**

| No. | 列名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | EMPNO | NUMBER(4) | 雇员编号，最多由4位数字所组成 |
| 2 | ENAME | VARCHAR2(10) | 雇员姓名 |
| 3 | JOB | VARCHAR2(9) | 雇员职位 |
| 4 | MGR | NUMBER(4) | 每个雇员的领导编号 |
| 5 | HIREDATE | DATE | 入职日期，里面包含有日期和时间 |
| 6 | SAL | NUMBER(7,2) | 基本工资，小数点最多只占2位，整数最多只占5位 |
| 7 | COMM | NUMBER(7,2) | 佣金，一般只有销售才会存在佣金的概念 |
| 8 | DEPTNO | NUMBER(2) | 部门编号，值的是每个雇员对应的雇员编号信息 |

```sql
SQL> select * from emp;

     EMPNO ENAME                JOB                       MGR HIREDATE              SAL       COMM     DEPTNO
---------- -------------------- ------------------ ---------- -------------- ---------- ---------- ----------
      7369 SMITH                CLERK                    7902 17-12月-80            800                 20
      7499 ALLEN                SALESMAN                 7698 20-2月 -81           1600        300      30
      7521 WARD                 SALESMAN                 7698 22-2月 -81           1250        500      30
      7566 JONES                MANAGER                  7839 02-4月 -81           2975                 20
      7654 MARTIN               SALESMAN                 7698 28-9月 -81           1250       1400      30
      7698 BLAKE                MANAGER                  7839 01-5月 -81           2850                 30
      7782 CLARK                MANAGER                  7839 09-6月 -81           2450                 10
      7788 SCOTT                ANALYST                  7566 19-4月 -87           3000                 20
      7839 KING                 PRESIDENT                     17-11月-81           5000                 10
      7844 TURNER               SALESMAN                 7698 08-9月 -81           1500          0      30
      7876 ADAMS                CLERK                    7788 23-5月 -87           1100                 20
      7900 JAMES                CLERK                    7698 03-12月-81            950                 30
      7902 FORD                 ANALYST                  7566 03-12月-81           3000                 20
      7934 MILLER               CLERK                    7782 23-1月 -82           1300                 10

已选择14行。
```

**3、工资等级表：salgrade**

| No. | 列名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | GRADE | NUMBER | 工资等级编号 |
| 2 | LOSAL | NUMBER | 此等级的最低工资 |
| 3 | HISAL | NUMBER | 此等级的最高工资 |

```sql
SQL> select * from salgrade;

     GRADE      LOSAL      HISAL
---------- ---------- ----------
         1        700       1200
         2       1201       1400
         3       1401       2000
         4       2001       3000
         5       3001       9999
```

工资等级里面的工资确定工资级别是要根据：losal<=工资<=hisal来决定。

**4、工资表：bonus**

| No. | 列名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | ENAME | VARCHAR2(10) | 雇员姓名 |
| 2 | JOB | VARCHAR2(9) | 职位 |
| 3 | SAL | NUMBER | 基本工资 |
| 4 | COMM | NUMBER | 佣金 |

在bonus表里面没有任何的数据存在。

## 总结

1、学习SQL重点在于DML操作上。