# 第 17 节 数据表的创建与管理

在之前使用的都是系统提供的数据表，而在实际的开发之中往往需要进行自定义数据表，但是需要注意的是：数据表的定义属于SQL-DDL，其不受到事务的控制。在Oracle之中，如果你的事务未提交，并且发生了DDL操作，所有未提交的事务将自动提交。也就是说在实际的开发之中，数据表的创建要么是在系统定义的同时设置，或者是进行关闭服务的维护。

## 常用的数据类型

数据表是由一对的数据所组成，每一个数据都要求有自己对应的数据类型。所以在传统的关系型数据库开发之中，常用的数据类型有如下几种：
* VARCHAR2(n)：一般保存200个字以内的数据都会采用此类型，例如：姓名、家庭住址；
* NUMBER：直接编写NUMBER表示的就是数字（包括小数或者是整数）
  * NUMBER(n)：表示最多n为整数；
  * NUMBER(n,m)：表示m个小数位，n-m个整数位；
* DATE：描述日期时间（Oracle中的DATE类型是有时间数据的，而其他数据库的DATE只有日期）；
* CLOB：描述大文本数据（4G）；
* BLOB：描述二进制数据（4G），例如：图片、电影、音乐，一般情况下不会考虑BLOB类型；

## 创建数据表

创建数据表的基本语法如下：
```sql
CREATE TABLE 表名称 (
    列名称 数据类型 [DEFAULT 默认值],
    列名称 数据类型 [DEFAULT 默认值],
        ...
    列名称 数据类型 [DEFAULT 默认值]
);
```

**范例：**进行数据表的创建
```sql
CREATE TABLE member(
    mid         VARCHAR2(20),
    name        VARCHAR2(50)    DEFAULT 'CHANGE ON UPDATE',
    age         NUMBER(3),
    salary      NUMBER(10,2),
    birthday    DATE            DEFAULT SYSDATE,
    note        CLOB
);
```

当数据表创建成功之后就可以是使用`SELECT * FROM tab;`查看所有的数据表名称，或者使用`DESC 表名称;`查看数据组成的结构。

**范例：**向数据表之中进行数据的存储
```sql
INSERT INTO member (mid,name,age,salary,birthday,note) VALUES ('ALPHA','ANNIE',25,4000,TO_DATE('2000-04-12','yyyy-mm-dd'),'NOTHING');
INSERT INTO member (mid,age,salary,birthday,note) VALUES ('BATE',250,500,TO_DATE('2017-12-04','yyyy-mm-dd'),'SOMTHING');
```

此时添加数据时使用默认值只是数据库本身的一种实现，而在实际的开发之中，默认值的意义不是很大，这些内容的设置往往是通过程序完成的。

## 表的复制

在之前操作都是自定义数据表，在数据库之中还支持一种比较方便的方式：复制表。可以根据已有的数据表（也可能是查询语句）来进行数据表的动态创建。
```sql
CREATE TABLE 表名称 AS 子查询;
```

**范例：**将20部门的雇员复制到emp20表中
```sql
CREATE TABLE emp20 AS SELECT * FROM emp WHERE deptno=20;
```

**范例：**将emp的表结构复制到empnull表中
* 此时只需要编写一个绝对不可能满足的查询条件即可；
```sql
CREATE TABLE empnull AS SELECT * FROM emp WHERE 1=2;
```

**范例：**以上的表都是根据已有的表进行了复制，甚至在做表创建的时候也可以操作的更加复杂一点，直接采用子查询结果创建。
* 创建一张数据表，并且要求将部门表数据的统计结果保存到此表中
```sql
CREATE TABLE deptstat
AS
SELECT d.deptno,d.dname,d.loc,temp.count
FROM dept d,(
    SELECT deptno dno,COUNT(empno) count
    FROM emp
    GROUP BY deptno) temp
WHERE d.deptno=temp.dno;
```

如果需要进行统计信息，为了方便统计信息的快速查询，可以采用此种模式，将长时间的操作结果保存在数据表里面，就可以方便浏览。

## 修改表名称

对于修改表名称此类的操作在开发之中是不建议使用的，所以这样的功能也只有Oracle支持。对于表名称的修改，首先必须清楚数据字典的概念。

**数据字典：**是指数据库会自动进行相关数据对象的记录。
例如：`SELECT * FROM tab;`，这条语句是用来查看一个用户下的所有用户表信息的，并且它所能返回的内容也十分有限，实际上这就是一个数据字典的信息显示。

所谓的数据字典可以理解为一张系统维护的数据表，当用户创建了一张数据表之后，在数据库里面会自动的将这个表的蒙城以及表的相关信息都保存在一张系统维护的数据表里面。而所谓表名的更新就是进行了数据字典信息的修改。但是该数据字典是由系统维护的，所以用户不具备数据字典的直接更新能力，所以只能够采用Oracle提供的一系列命令完成。在Oracle之中数据字典一共分为三类：
* USER_*：表示用户具有的数据字典信息；
* DBA_*：表示数据库管理员具有的数据字典信息；
* ALL_*：表示用户和管理员都可以访问的数据字典信息。

**范例：**查询数据表保存的字典信息
```sql
SELECT * FROM USER_TABLES;
```

**范例：**修改表名称
* 语法：`RENAME 旧名称 TO 新名称;`
```sql
RENAME member TO person;
```

## 截断表

假设现在要清楚某张数据表的全部数据，最直接的方式就是`DELETE FROM 表名称;`。但是此时数据表所占用的资源依旧会被保存下载，这些资源可能保存：数据索引信息、约束信息，也就是说标的数据删除之后并不是立即释放掉所有的资源。如果想要立即释放掉所有的资源，那么就需要使用_**截断表**_的操作。表一旦被截断，数据就无法再进行回滚了。

**范例：**截断表
```sql
TRUNCATE TABLE person;
```

表被截断之后该表中的所有资源将彻底释放，并且无法进行事务的回滚处理操作，一般都在清空数据表的时候使用，但是一般在实际开发中，数据表清空=花样作死。

## 删除表

数据表不再使用之后可以进行删除，表一旦被删除之后默认情况下（大部分情况）是无法进行恢复的。删除表的语法如下：
```sql
DROP TABLE 表名称;
```

**范例：**删除数据表
```sql
DROP TABLE empnull;
```

在Oracle 10g之前，进行数据表删除操作是不可逆的。但是在Oracle 10g之后提供了数据表删除后恢复机制。

## 闪回操作

从Oracle 10g开始默认情况下每当执行了一次表的删除处理都会留下一些`BIN$HK2KOZ4mQsSK0GsGGAs7Gw==$0`形式的数据表，实际上这个就属于FlashBack（闪回）技术，相当于提供了一个回收站的功能。

1. 在之前版本查看回收站的操作：`SHOW RECYCLEBIN`
  * 正常情况下可以采用数据字典查看：
  ```sql
  SELECT object_name,original_name,droptime FROM user_recyclebin;
  ```
  通过查询可以发现之前所删除的数据表的信息都可以在回收站之中查看到。
2. 通过回收站可以进行已删除表的恢复
```sql
FLASHBACK TABLE person TO BEFORE DROP;
```
3. Oracle还提供了直接删除（不经过回收站）的功能
```sql
DROP TABLE person PURGE;
```
4. 从回收站中删除表
```sql
PURGE TABLE person;
```
5. 清空回收站
```sql
PURGE RECYCLEBIN;
```

## 修改表结构

修改表结构这一功能不建议使用，特别是在已经上线运行中的项目，如果不是必须不要进行这样的操作。

**范例：**准备出一张数据表
```sql
CREATE TABLE member (
    mid     NUMBER,
    name    VARCHAR2(20)
);
INSERT INTO member (mid,name) VALUES (1,'ALPHA');
INSERT INTO member (mid,name) VALUES (2,'BATE');
```

现在表已经创建完成，并且在使用中。但是此时开发者发现表中的字段缺少一些，希望可以扩充一些字段。实际上表的修改就是字段的修改。
```sql
ALTER TABLE 表名称 ADD (列名称 数据类型 [DEFAULT 默认值],...);
```

**范例：**为表中追加一个age数据列
```sql
ALTER TABLE member ADD (age NUMBER(3));
```

此时，新增的字段默认值为null。

**范例：**为表中追加一个sex数据列，设置默认值为'男'
```sql
ALTER TABLE member ADD (sex VARCHAR2(10) DEFAULT '男');
```
此时，新增的字段默认值为'男'。

如果发现表中的数据列设置不合适的时候可以对其进行修改：
```sql
ALTER TABLE 表名称 MODIFY (列名称 数据类型 [DEFAULT 默认值],...);
```

**范例：**将name的长度修改为50
```sql
ALTER TABLE member MODIFY (name VARCHAR2(50));
```

也可以对数据列进行删除：
```sql
ALTER TABLE 表名称 DROP COLUMN 字段名称;
```

**范例：**删除掉age的数据列
```sql
ALTER TABLE member DROP COLUMN age;
```

数据库对象（DDL）操作只有三类语法：
* CREATE 对象类型 对象名称;
* ~~_DROP 对象类型 对象名称;_~~
* ~~_ALTER 对象类型 对象名称;_~~