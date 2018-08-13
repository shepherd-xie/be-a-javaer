# 第 21 节 视图的定义与使用

如果是在正规的企业级开发过程之中，会有专门的数据库设计人员为项目进行数据库的设计，同时还会为开发者提供所有的查询封装。在编写SQL过程之中查询是最痛苦的语句。在数据库设计的概念里面就提供有一种视图的定义，它的主要功能是封装复杂的查询语句，也就是说利用视图就可以实现数据的复杂查询操作。创建视图可以采用如下的语法完成：
```sql
CREATE [OR REPLACE] VIEW 视图名称 AS 子查询
[WITH CHECK OPTION] [WITH READ ONLY];
```

OR REPLACE表示创建视图时如果视图不存在则创建视图，如果视图存在则使用当前语句替换该视图。

**范例：**实现一个包含有复杂操作的视图
```sql
CREATE OR REPLACE VIEW myview
    AS
SELECT d.deptno,d.dname,d.loc,temp.count
FROM dept d,(
    SELECT deptno dno,COUNT(empno) count
    FROM emp
    GROUP BY deptno) temp
WHERE d.deptno=temp.dno(+);
```

在最初的Oracle版本里面实际上scott是具备有视图创建权限的，但是在新版本之中将scott视图创建权限给剥削了，所以需要重新授权：
* 使用sys登录：`CONN sys/change_on_install AS SYSDBA;`；
* 进行授权控制：`GRANT CREATE VIEW TO scott;`；
* 切换回scott：`CONN scott/tiger;`；

当视图创建完成之后，就可以直接查询视图：
```sql
SELECT * FROM myview;
```

在实际之中数据表的查询都是比较复杂的，所以一般在一些传统的技术开发里面，视图的数量往往会超过数据表的数量，但是随着现在的一些开发（很难找到所谓的专业的数据库开发人员），这样一来所有编写复杂查询的语句的任务又回到程序员身上了，所以很多程序员为了代码的修改方便，都不再使用视图了。

对于视图而言，本身支持的功能还是很多的，但是视图只是一个查询的结果，并不表示真实的数据，所以理论上视图是不应该被修改的，可是默认情况下不进行任何的配置，视图中的数据是允许修改的。

**范例：**创建一个包含有20部门雇员信息视图
```sql
CREATE OR REPLACE VIEW myview
    AS
SELECT * FROM emp WHERE deptno=20;
```

**范例：**更新雇员编号为7369的部门编号为30
```sql
UPDATE myview SET deptno=30 WHERE empno=7369;
```

此时直接修改了视图中的数据并且影响了原始的数据，最关键的是修改的字段为视图的创建条件字段，很明显这样的做法是不合理的。所以为例保证字段的内容不被改变，这个时候可以考虑使用一个语句：`WITH CHECK OPTION`。

**范例：**创建一个不允许修改创建字段的视图
```sql
CREATE OR REPLACE VIEW myview
    AS
SELECT * FROM emp WHERE deptno=20
WITH CHECK OPTION;
```

这个时候将无法修改视图中的deptno字段，可是其他的字段依然允许用户修改。为了保证视图不可更改可以追加`WITH READ ONLY`，将其配置为只读视图。
```sql
CREATE OR REPLACE VIEW myview
    AS
SELECT * FROM emp WHERE deptno=20
WITH READ ONLY;
```
