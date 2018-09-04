# 第 18 节 约束的创建与管理

当数据表创建完成的时候只是确定了要操作的数据表的存储结构，真正要进行使用的时候要确定其中存放时第有效数据。例如：性别的描述、年龄的描述、学号的描述等，所以在传统的关系型数据库里面为了保证这些数据的完整性，就提拱了一套自己的检测机制：约束。

## 约束的基本概念

约束是保证数据完整性的一种手段，而后根据功能约束一共分为六种，其中有一种称为数据类型的约束，例如：字段的类型为NUMBER则不能存入其他类型的数据。而处理这类约束之外其余的五种约束分别是：
* 非空约束（NOT NULL、NK）：保证数据表中某字段的内容不允许为null；
* 唯一约束（UNIQE KEY、UK）：该列上的数据不允许重复；
* _**主键约束（PRIMARY KEY、PK）：**_非空约束 + 唯一约束；
* 检查约束（CHECK、CK）：设置判断条件，只允许存储满足条件的数据；
* _**外键约束（FOREIGN KEY、FK）：**_设置父子表之间的约束关系。

约束有一个最基本的概念：只有满足约束条件的数据才能够被保存在数据库之中。

对于同一个字段上设置有多个约束条件时，这些约束条件是要同时满足的。也就是说，表中的约束越多，表的性能越差。但是对于约束的使用需要考虑两种情况：
* 传统的软件开发：由于其使用到的服务器设备有限，所以约束可以适当完善；
* 互联网时代的软件开发：基本上唯一可以使用的约束就是主键约束了。

## 非空约束

非空约束严格来讲属于一种特殊的约束，因为其设置约束的形式与其他的约束相同，但是其处理的流程有一些差别，如果要想使用非空约束，则在创建表的时候在指定字段后追加`NOT NULL`。
```sql
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50) NOT NULL
);
```

此时name字段设置了非空约束，这要就表示在进行name字段的数据更新时必须设置内容。

**范例：** 正确的进行member数据增加
```sql
INSERT INTO member(mid,name) VALUES ('ALPHA','ALPHA GO');
```

**范例：** 错误示范
```
INSERT INTO member(mid) VALUES ('BETA');
INSERT INTO member(mid,name) VALUES ('BETA',null);
```

此时由于没有在name上设置内容，所以在数据增加的时候会出现如下的错误提示停息：`ORA-01400: 无法将 NULL 插入 ("SCOTT"."MEMBER"."NAME")`。

可以发现当数据更新时，如果违反了非空约束，则会明确的告诉用户代码出错的位置所在。

## 唯一约束

唯一约束的主要特征指的是数据表中某个字段的内容不允许重复。设置唯一约束可以在创建表时在字段后追加`UNIQUE`。
```sql
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50) NOT NULL,
    email   VARCHAR2(50) UNIQUE
);
```

**范例：** 添加正确的数据
```sql
INSERT INTO member(mid,name,email) VALUES ('ALPHA','ALPHA GO','alpha@gmail.com');
INSERT INTO member(mid,name,email) VALUES ('ALPHA','ALPHA GO',null);
```

**范例：** 向数据表中追加重复数据
```
INSERT INTO member(mid,name,email) VALUES ('BETA','BETA GO','alpha@gmail.com');
INSERT INTO member(mid,name,email) VALUES ('BETA','BETA GO',null);
```

例外的是，在设置唯一约束的时候，`null`不在唯一约束的限制范围之内。

由于此时要增加的email数据重复了，所以执行以上语句时会出现如下的错误提示信息：`ORA-00001: 违反唯一约束条件 (SCOTT.SYS_C0011575)`。此时无法从错误信息中获取到清楚的描述信息。

实际上所有的约束在Oracle数据库之中都会称为一个个独立的数据库对象，如果没有为对象设置独立的名字，那么会由系统为其分配一个唯一的名称（SYS_C0011575）进行该对象的标注。所以如果要想查看约束的具体信息，就需要通过数据字典进行观察：
* `SELECT * FROM user_constraints;`：查看约束与表的对应关系；
* `SELECT * FROM user_cons_columns;`：查看约束与表中列的对应关系；

```sql
COL owner FOR A10;
COL constraint_name FOR A15;
COL table_name FOR A10;
COL column_name FOR A10;
SELECT owner,constraint_name,table_name,column_name 
FROM user_cons_columns;
```

```dos
OWNER      CONSTRAINT_NAME TABLE_NAME COLUMN_NAM
---------- --------------- ---------- ----------
SCOTT      SYS_C0011575    MEMBER     EMAIL
SCOTT      SYS_C0011574    MEMBER     NAME
SCOTT      FK_DEPTNO       EMP        DEPTNO
SCOTT      PK_EMP          EMP        EMPNO
SCOTT      PK_DEPT         DEPT       DEPTNO
```
为例简化约束的错误查看，最好的做法是明确的为约束设置一个名字，而如果要想设置约束的名字，那么就不能直接在列上设置约束，需要增加新的配置项。
```sql
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50) NOT NULL,
    email   VARCHAR2(50),
    CONSTRAINT uk_email UNIQUE(email)
);
```

此时，抛出的错误信息：`ORA-00001: 违反唯一约束条件 (SCOTT.UK_EMAIL)`。以后在设计数据库脚本的时候，为了方便约束的维护，每一个约束都要设置一个具体的约束名称。

## 主键约束

主键约束简单的说就是`非空约束 + 唯一约束`，也就是说用做_唯一标识_使用，例如：中国公民的身份证编号、某学校学生的学号，此类数据不可重复、不可为空。

**范例：** 定义主键约束
```sql
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50) NOT NULL,
    CONSTRAINT pk_mid PRIMARY KEY(mid)
);
```

**范例：** 添加正确的数据
```sql
INSERT INTO member(mid,name) VALUES ('ALPHA','ALPHA GO');
```

**范例：** 错误的数据
* 情况一：追加的主键内容为空（错误提示：`ORA-01400: 无法将 NULL 插入 ("SCOTT"."MEMBER"."MID")`）
```
INSERT INTO member(name) VALUES ('BETA GO');
INSERT INTO member(mid,name) VALUES (null,'BETA GO');
```
* 情况二：设置的主键重复(错误提示：`ORA-00001: 违反唯一约束条件 (SCOTT.PK_MID)`)
```
INSERT INTO member(mid,name) VALUES ('ALPHA','BETA GO');
```

通过提示的错误信息以及错误编号更加可以印证之前的结论：`主键约束 = 唯一约束 + 非空约束`。

正常情况下一张数据表仅有一个主键，但数据库也允许在两个或多个字段上同时设置主键，称为：~~_复合主键_~~。

**范例：** 定义复合主键
```
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50),
    CONSTRAINT pk_mid_name PRIMARY KEY(mid,name)
);
```

当设置的多个字段为主键时表示当且仅当多个字段的值完全一致时才会违反唯一约束。

**范例：** 正确的数据
```sql
INSERT INTO member(mid,name) VALUES ('ALPHA','ALPHA GO');
INSERT INTO member(mid,name) VALUES ('ALPHA','BETA GO');
INSERT INTO member(mid,name) VALUES ('BETA','ALPHA GO');
```

**范例：** 错误的数据
```
INSERT INTO member(mid,name) VALUES ('ALPHA','ALPHA GO');
```

从正常的开发角度来讲，极度不建议使用复合主键。

## 检查约束

检查约束的本质在于设置一系列的过滤条件，当满足了这些过滤条件之后才可以保证数据更新，例如：在进行年龄设置的时候年龄应当在0~255之间，在设置性别时只能设置男、女。

**范例：** 设置检查约束
```sql
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50),
    age     NUMBER(3),
    sex     VARCHAR2(10),
    CONSTRAINT pk_mid PRIMARY KEY(mid),
    CONSTRAINT ck_age CHECK(age BETWEEN 0 AND 255),
    CONSTRAINT ck_sex CHECK(sex IN ('男','女'))
);
```

**范例：** 增加正确的数据
```sql
INSERT INTO member(mid,name,age,sex) VALUES ('ALPHA','ALPHA GO',0,'女');
INSERT INTO member(mid,name,age,sex) VALUES ('BETA','BETA GO',255,'男');
```

**范例：** 错误的数据
```
INSERT INTO member(mid,name,age,sex) VALUES ('GAMMA','GAMMA GO',-1,'不正常');
INSERT INTO member(mid,name,age,sex) VALUES ('DELTA','DELTA GO',256,'正常');
```

此时，有多个字段同时违反了检查约束，但是很明显，这些检查约束并不是一次性完成的，而是依次对数据进行检查的。所以，设置的检查约束越多，表的性能越差。所以在正常的程序开发之中，绝大多数的数据有效性检查都会在程序中处理。

## 外键约束

之前的几种约束作用的范围都是在单表上进行操作，而外键约束描述的是两张数据表之间的关联数据。例如：在之前用到的emp表与dept表，一个部门有多个雇员的关系。

**范例：** 建立人与图书的关系
```sql
DROP TABLE member PURGE;
DROP TABLE book PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50),
    CONSTRAINT pk_mid PRIMARY KEY(mid)
);
CREATE TABLE book (
    bid     NUMBER,
    title   VARCHAR2(50),
    mid     VARCHAR2(50),
    CONSTRAINT pk_bid PRIMARY KEY(bid)
);
```

创建了数据表之后向表中追加数据。
```sql
INSERT INTO member(mid,name) VALUES ('ALPHA','ALPHA GO');
INSERT INTO member(mid,name) VALUES ('BETA','BETA GO');
INSERT INTO book(bid,title,mid) VALUES (1001,'Introduction to Computer Science','ALPHA');
INSERT INTO book(bid,title,mid) VALUES (1002,'Introduction to Software Engineering','ALPHA');
INSERT INTO book(bid,title,mid) VALUES (1003,'Electronic engineering','BETA');
INSERT INTO book(bid,title,mid) VALUES (1004,'Digital circuits','BETA');
```

此时也有可能添加如下的数据。
```
INSERT INTO book(bid,title,mid) VALUES (1005,'computer network','GAMMA');
INSERT INTO book(bid,title,mid) VALUES (1006,'data structure','DELAT');
```

这个时候可以发现，在member表中并没有`GAMMA`和`DELAT`的mid。这个数据显然不应该保存到book表中，`book.mid`的取值范围应该由`member.mid`决定。想要实现这种关系的约束就要依靠外键。

**范例：** 使用外键规范做法
```sql
DROP TABLE member PURGE;
DROP TABLE book PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50),
    CONSTRAINT pk_mid PRIMARY KEY(mid)
);
CREATE TABLE book (
    bid     NUMBER,
    title   VARCHAR2(50),
    mid     VARCHAR2(50),
    CONSTRAINT pk_bid PRIMARY KEY(bid),
    CONSTRAINT fk_mid FOREIGN KEY(mid) REFERENCES member(mid)
);
```

此时如果追加关联数据的时候没有指定的数据与之对应则会抛出`ORA-02291: 违反完整约束条件 (SCOTT.FK_MID) - 未找到父项关键字`错误信息。

虽然外键在整体上起到了约束作用，但是需要注意一些问题。

1. 一旦设置了外键关系后，再删除主表之前就必须先删除子表，否则无法删除`ORA-02449: 表中的唯一/主键被外键引用`。
```sql
DROP TABLE book PURGE;
DROP TABLE member PURGE;
```
或忽略外键关系，强制删除。
```sql
DROP TABLE member CASCADE CONSTRAINT;
```
2. 对于已存在的记录，必须先删除子表记录后才能删除父表记录。但是这样的操作十分麻烦，为了解决这个问题，可以在设置级联操作，级联操作有两种：
  * 级联删除：`ON DELETE CASCADE`；
  ```sql
  CREATE TABLE book (
      bid     NUMBER,
      title   VARCHAR2(50),
      mid     VARCHAR2(50),
      CONSTRAINT pk_bid PRIMARY KEY(bid),
      CONSTRAINT fk_mid FOREIGN KEY(mid) REFERENCES member(mid) ON DELETE CASCADE
  );
  ```
  * 级联更新：`ON DELETE SET NULL`，如果被删除则将子表字段设置为`null`；
  ```sql
  CREATE TABLE book (
      bid     NUMBER,
      title   VARCHAR2(50),
      mid     VARCHAR2(50),
      CONSTRAINT pk_bid PRIMARY KEY(bid),
      CONSTRAINT fk_mid FOREIGN KEY(mid) REFERENCES member(mid) ON DELETE SET NULL
  );
  ```

## 修改约束

_**设置原则：**_在任何的系统数据库设计的时候，当你的数据库表创建完成之后一定要将约束同时建立完毕，并且不要进行约束的变更。

为了演示约束的修改处理，创建一张简单的数据表：

**范例：** 建立数据表
```sql
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     VARCHAR2(50),
    name    VARCHAR2(50)
);
INSERT INTO member(mid,name) VALUES ('ALPHA','ALPHA GO');
INSERT INTO member(mid,name) VALUES ('ALPHA','BETA GO');
INSERT INTO member(mid,name) VALUES ('BETA',null);
```

1\. 为表追加约束：

* 语法：`ALTER TABLE 表名称 ADD CONSTRAINT 约束名称 约束类型(字段)`；

**范例：** 为member表追加主键约束
  * 如果数据表之中的数据有违法主键的情况出现，那么该约束是无法添加的；
```sql
ALTER TABLE member ADD CONSTRAINT pk_mid PRIMARY KEY(mid);
```

以上语法是能够追加非空之外的约束，如果想要增加非空约束，只能够通过修改表结构的操作完成；
```sql
ALTER TABLE member MODIFY (name VARCHAR2(50) NOT NULL);
```

2\. 删除表约束：

* 语法：`ALTER TABLE 表名称 DROP CONSTRAINT 约束名称;`

**范例：** 删除主键约束
```sql
ALTER TABLE member DROP CONSTRAINT pk_mid;
```
