# 第 14 节 数据更新操作

SQL语句之中DML操作主要是分为两类：查询、更新。对于查询而言需要进行一些的分析，但是更新的操作相比较查询而言会比较容易一些，其操作语法也基本上是固定的。

但是需要有一点注意：由于在以后的开发之中还会继续使用到scott中的数据表，所以为了保证scott数据表中的数据不被破坏，所以要对该表做一个复制。

**范例：**复制`scott.emp`表为`scott.myemp`
```sql
CREATE TABLE myemp AS SELECT * FROM emp;
```

对于数据更新操作在SQL之中一共分为三种：增加（INSERT）、修改（UPDATE）、删除（DELETE）。

## 数据增加

如果要向已有的数据表之中进行数据的增加，其采用的语法可以有两种：
* 【推荐】完整语法格式：
```sql
INSERT INTO 表名称(列名称,列名称,...) VALUES (内容1,内容2,...);
```
* 简化语法（不写列名称）：
```sql
INSERT INTO 表名称 VALUES (内容1,内容2,...);
```

在进行内容追加的时候常见的几种类型：
* 字符串：要求使用“'”声明，例如：'hello'；
* 数字：直接进行编写；
* 日期：当前日期使用SYSDATE描述，如果要自定义日期则可以使用TO_DATE()函数换换。

**范例：**向myemp表中追加一行新的记录
```sql
INSERT INTO myemp(empno,sal,ename,hiredate,job,mgr,comm,deptno) VALUES (8888,9999.99,'达天',TO_DATE('1969-10-10','yyyy-mm-dd'),'程序猿',7369,10,40);
```
如果你是用的是简化格式，并不是简单的将列名称取消掉而已。
```sql
INSERT INTO myemp VALUES (7777,'种天','程序猿',7369,TO_DATE('1969-10-10','yyyy-mm-dd'),9999.99,10,40);
```
如果这样书写会使得程序的维护变得异常的麻烦，所以在实际的开发之中只要进行数据的增加操作，都必须明确的写出具体的数据列。

但是需要注意的是，如果现在某些字段你既不想设置数据（null）有两种做法：
* 做法一：在增加的时候不设置具体的字段内容；
```sql
INSERT INTO myemp(empno,sal,ename,hiredate) VALUES (7771,9999.99,'添天',TO_DATE('1969-10-10','yyyy-mm-dd'));
```
* 做法二：明确的设置内容为null
```sql
INSERT INTO myemp(empno,sal,ename,hiredate) VALUES (7772,9999.99,'成天',null);
```

在以后编写增加语句的时候一定要使用完整模式，不需要的字段可以不写，默认为空。