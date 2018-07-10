# 第 7 节 查询排序

在之前的查询里面只使用了SELECT、FROM、WHERE，但是如果需要针对于查询后的结果按照指定的数据列进行排序操作，就必须使用ORDER BY子句。

当使用查询操作的时候，默认情况下他会按照数据的插入顺序进行排序显示。

**范例：**执行以下的操作

```sql
SELECT * FROM emp;
```

如果要针对于指定的列内容实现排序，就必须采用ORDER BY子句完成。

```sql
【③控制要显示的数据列】SELECT [DISTINCT] * | 列名称 [别名], 列名称 [别名], ...
【①确定数据来源】 FROM 表名称 [别名]
【②确定满足条件的数据行】[WHERE 过滤条件(s)]
【④针对查询结果进行排序】[ORDER BY 字段 [ASC | DESC], 字段 [ASC | DESC],...];
```

ORDER BY子句是在SELECT子句之后执行，而ORDER BY子句可以使用SELECT子句定义的别名。

对于排序的方式有两种：

* ASC（默认）：按照升序的方式排列；
* DESC：按照降序的方式排列。

**范例：**查询所有的雇员信息，要求按照工资由高到低排序

```sql
SELECT * FROM emp ORDER BY sal DESC;
```

**范例：**查询所有销售人员的信息，要求按照雇佣日期由早到晚排序

```sql
SELECT * FROM emp WHERE job='SALESMAN' ORDER BY hiredate;
```

以上只是针对于一个字段的排序，那么也可以同时设置多个排序字段。 

**范例：**要求按照工资由高到低排序，如果工资相同，则按照雇佣日期由早到晚排序

```sql
SELECT * FROM emp ORDER BY sal DESC, hiredate ASC;
```

```sql
SELECT * FROM emp ORDER BY sal DESC, hiredate;
```

ORDER BY子句既然在SELECT子句之后执行，那么就可以使用SELECT子句里定义的别名。

**范例：**查询每个雇员的编号、姓名、年薪，按照年薪由低到高排序

```sql
SELECT empno, ename, sal*12 income 
FROM emp
ORDER BY income;
```

在整个SQL查询结构之中，只有ORDER BY子句可以调用SELECT定义的别名。

## 总结

1、使用ORDER BY查询的时候，排序是在最后完成的；

2、ORDER BY子句是最后一个执行的子句；

3、在ORDER BY之中可以设置多个排序的字段；

4、ORDER BY有两种裴谞模式：ASC、DESC；

5、ORDER BY是唯一一个可以使用SELECT子句定义别名的子句。