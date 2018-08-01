# 第 20 节 序列的定义与使用

Oracle序列是一个最为常用的数据对象类型，所谓的序列本质上值得就是自动增长列的配置实现，在很多的数据库上都提供有自动增长列的概念。例如：Access数据库提供有自动编号的概念。

但是Oracle从开始到11g都没有提供这样渐变的自动增长列的设置，他所提供的是一种手工的控制序列的模式来实现的自动增长列（用户需要自己手工控制），在Oracle 12c开始才真正追加了自动增长列的配置。

进行序列的创建，可以采用如下的语法完成：
```sql
CREATE SEQUENCE 序列名称
[START WITH 开始值]
[INCREMENT BY 步长]
[MAXVALUE 最大值 | NOMAXVALUE]
[MINVALUE 最小值 | NOMINVALUE]
[CYCLE | NOCYCLE]
[CACHE 缓存个数 | NOCACHE]
```

以上的众多选项其默认设置如下
* 开始值（START WITH）：1；
* 步长：1；
* 最大值：NOMAXVALUE；
* 最小值：1；
* 非循环序列：NOCYCLE；
* 缓冲个数：20；

**范例：**创建一个序列
```sql
CREATE SEQUENCE myseq;
```

当一个序列创建成功之后会自动将此序列的信息保存在数据字典表之中（user_sequences）。

**范例：**查询数据字典
```sql
SELECT * FROM user_sequences;
```

当序列创建完成之后如果要想使用该序列，则可以采用序列中定义的两个数据伪列：
* `序列名称.nextval`：获取下一个增长值，也就是说当前内容加上步长；
* `序列名称.currval`：获取序列当前的内容；
  * 一定要先调用`nextval`后才可以执行`currval`否则会出现`ORA-08002: 序列 MYSEQ.CURRVAL 尚未在此会话中定义`；

**范例：**序列的增长
```sql
SELECT myseq.nextval FROM dual;
```

如果要想将序列结合数据表一起使用，那么只能够在增加语句上操作。

**范例：**创建数据表
```sql
DROP TABLE member PURGE;
CREATE TABLE member (
    mid     NUMBER,
    name    VARCHAR2(50),
    CONSTRAINT pk_mid PRIMARY KEY(mid)
);
```

**范例：**编写增加语句，设置mid的时候使用序列处理
```sql
INSERT INTO member(mid,name) VALUES (myseq.nextval,'ALPHA');
```