# 第 13 节 综合案例：复杂查询

1. 列出与“SCOTT”从事相同工作的所有员工及部门名称，部门人数，平均工资。
  * 确定要使用的数据表：
    * emp表：可以获取员工信息；
    * dept表：部门名称；
    * emp表：员工的统计信息；
  * 确定已知的关联字段：
    * 雇员和部门：`emp.deptno=deptno.deptno`；
  * **第一步：**首先查询出SCOTT的职位；
  ```sql
  SELECT job FROM emp WHERE ename='SCOTT';
  ```
  * **第二步：**根据SCOTT的职位找到具备此职位信息的员工；
  ```sql
  SELECT e.empno,e.ename,e.job
  FROM emp e
  WHERE e.job=(
      SELECT job FROM emp WHERE ename='SCOTT')
      AND e.ename<>'SCOTT';
  ```
  * **第三步：**要查询出该支援对应的部门名称；
  ```sql
  SELECT e.empno,e.ename,e.job,d.dname
  FROM emp e,dept d
  WHERE e.job=(
      SELECT job FROM emp WHERE ename='SCOTT')
      AND e.ename<>'SCOTT'
      AND e.deptno=d.deptno;
  ``` 
  * **第四步：**需要统计出部门的人数和平均工资；
    * GROUP BY使用限制：
      * 如果一个查询语句之中没有GROUP BY，SELECT子句只能够出现统计函数；
      * 如果查询语句之中存在有GROUP BY，则SELECT子句可以出现分组字段和统计函数；
      * 统计函数允许嵌套，嵌套之后的SELECT子句不允许出现任何字段，包括分组字段； 
  * 此时需要进行分组统计，但是已有的查询明显是不能够进行直接的分组操作的，那么就在FROM子句中编写子查询。
  ```sql
  SELECT e.empno,e.ename,e.job,d.dname,temp.count,temp.avg
  FROM emp e,dept d,(
      SELECT deptno,COUNT(empno) count,AVG(sal) avg
      FROM emp
      GROUP BY deptno) temp
  WHERE e.job=(
      SELECT job FROM emp WHERE ename='SCOTT')
      AND e.ename<>'SCOTT'
      AND e.deptno=d.deptno
      AND e.deptno=temp.deptno;
  ```
2. 列出薪金高于在部门30工作的所有员工的薪金以及高于公司平均工资的员工姓名和薪金、部门名称、部门平均工资、个人工资等级。
  * 确定要使用的数据表：
    * emp表：员工姓名和薪金；
    * dept表：部门名称；
    * emp表：部门平均工资；
    * salgrade表：个人工资等级；
  * 确定已知的关联字段：
    * 雇员和部门：`emp.deptno=deptno.deptno`；
    * 雇员与工资等级：`emp.sal BETWEEN salgrade.losal AND salgrade.hisal`；
  * **第一步：**查询所有在30部门工作的员工工资
  ```sql
  SELECT sal FROM emp WHERE deptno=30;
  ```
  * **第二步：**查询出所有大于30部门员工工资的雇员信息，一定使用`>ALL`；
  ```sql
  SELECT e.ename,e.sal
  FROM emp e
  WHERE e.sal>ALL(
    SELECT sal FROM emp WHERE deptno=30);
  ```
  * **第三步：**高于公司平均工资
  ```sql
  SELECT e.ename,e.sal
  FROM emp e
  WHERE e.sal>ALL(
      SELECT sal FROM emp WHERE deptno=30)
      AND e.sal>(SELECT AVG(sal) FROM emp);
  ```
  * **第四步：**引入dept表查询出部门名称
  ```sql
  SELECT e.ename,e.sal,d.dname
  FROM emp e,dept d
  WHERE e.sal>ALL(
      SELECT sal FROM emp WHERE deptno=30)
      AND e.sal>(SELECT AVG(sal) FROM emp)
      AND e.deptno=d.deptno;
  ```
  * **第五步：**获取部门平均工资
  ```sql
  SELECT e.ename,e.sal,d.dname,temp.avg
  FROM emp e,dept d,(
      SELECT deptno,AVG(sal) avg
      FROM emp
      GROUP BY deptno) temp
  WHERE e.sal>ALL(
      SELECT sal FROM emp WHERE deptno=30)
      AND e.sal>(SELECT AVG(sal) FROM emp)
      AND e.deptno=d.deptno
      AND e.deptno=temp.deptno;
  ```
  * **第六步：**工资等级直接引入salgrade表
  ```sql
  SELECT e.ename,e.sal,d.dname,temp.avg,s.grade
  FROM emp e,dept d,(
      SELECT deptno,AVG(sal) avg
      FROM emp
      GROUP BY deptno) temp,salgrade s
  WHERE e.sal>ALL(
      SELECT sal FROM emp WHERE deptno=30)
      AND e.sal>(SELECT AVG(sal) FROM emp)
      AND e.deptno=d.deptno
      AND e.deptno=temp.deptno
      AND e.sal BETWEEN s.losal AND s.hisal;
  ```
3. 查询出所有雇员编号、姓名、职位、薪金、此职位的人数、此职位的人数、领导姓名、领导职位、领导所在职位的人数、雇员部门、雇员部门人数、领导部门、领导所在部门人数、雇员工资等级以及此等级的平均工资。
  * 确定要使用的数据表：
    * emp表：员工信息；
    * emp表：领导信息；
    * emp表：领导统计信息；
    * emp表：部门统计信息；
    * emp表：工资等级统计信息；
    * dept表：部门信息；
    * salgrade表：工资等级信息；
  * 确定已知的关联字段：
    * 雇员与部门：`emp.deptno=dept.deptno`；
    * 雇员与领导：`emp.mgr=memp.empno`；
    * 雇员与工作：`emp.sal BETWEEN salgrade.losal AND salgrade.hisal`；
  * **第一步：**查询出所有雇员编号、姓名、职位、薪金
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal
  FROM emp e;
  ```
  * **第二步：**查询出员工所对应职位的人数
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count
  FROM emp e,(
      SELECT job,COUNT(*) count
      FROM emp
      GROUP BY job) empjobtemp
  WHERE e.job=empjobtemp.job;
  ```
  * **第三步：**领导姓名、领导职位
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,m.ename,m.job
  FROM emp e,(
      SELECT job,COUNT(*) count
      FROM emp
      GROUP BY job) empjobtemp,emp m
  WHERE e.job=empjobtemp.job
      AND e.mgr=m.empno(+);
  ```
  * **第四步：**领导所在职位人数，应该按照领导的职位分组：
    * 首先要求筛选出所有的领导：
    ```sql
    SELECT DISTINCT mgr FROM emp;
    ```
    * 领导的职位要根据以上的返回结果根据job分组：
    ```sql
    SELECT job
    FROM emp
    WHERE empno IN (
        SELECT DISTINCT mgr FROM emp);
    ```
    * 随后以上面的职位信息进行分组的数据的筛选：
    ```sql
    SELECT job,COUNT(*)
    FROM emp
    WHERE job IN (
        SELECT job
        FROM emp
        WHERE empno IN (
            SELECT DISTINCT mgr FROM emp))
    GROUP BY job;
    ```
    * 需要将以上的查询安放到之前的查询之中：
    ```sql
    SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,m.ename,m.job,mgrjobtemp.count
    FROM emp e,(
        SELECT job,COUNT(*) count
        FROM emp
        GROUP BY job) empjobtemp,emp m,(
        SELECT job,COUNT(*) count
        FROM emp
        WHERE job IN (
            SELECT job
            FROM emp
            WHERE empno IN (
                SELECT DISTINCT mgr FROM emp))
        GROUP BY job) mgrjobtemp
    WHERE e.job=empjobtemp.job
        AND e.mgr=m.empno(+)
        AND m.job=mgrjobtemp.job;
    ```
  * **第五步：**要获取雇员的部门信息则引入部门表
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,m.ename,m.job,mgrjobtemp.count,d.dname
  FROM emp e,(
      SELECT job,COUNT(*) count
      FROM emp
      GROUP BY job) empjobtemp,emp m,(
      SELECT job,COUNT(*) count
      FROM emp
      WHERE job IN (
          SELECT job
          FROM emp
          WHERE empno IN (
              SELECT DISTINCT mgr FROM emp))
      GROUP BY job) mgrjobtemp,dept d
  WHERE e.job=empjobtemp.job
      AND e.mgr=m.empno(+)
      AND m.job=mgrjobtemp.job
      AND e.deptno=d.deptno;
  ```
  * **第六步：**雇员部门人数
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,m.ename,m.job,mgrjobtemp.count,d.dname,empdepttemp.count
  FROM emp e,(
      SELECT job,COUNT(*) count
      FROM emp
      GROUP BY job) empjobtemp,emp m,(
      SELECT job,COUNT(*) count
      FROM emp
      WHERE job IN (
          SELECT job
          FROM emp
          WHERE empno IN (
              SELECT DISTINCT mgr FROM emp))
      GROUP BY job) mgrjobtemp,dept d,(
      SELECT deptno dno,COUNT(*) count
      FROM emp
      GROUP BY deptno) empdepttemp
  WHERE e.job=empjobtemp.job
      AND e.mgr=m.empno(+)
      AND m.job=mgrjobtemp.job
      AND e.deptno=d.deptno
      AND e.deptno=empdepttemp.dno;
  ```
  * **第七步：**要求查询出领导对应的部门信息
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,m.ename,m.job,mgrjobtemp.count,d.dname,empdepttemp.count,d2.dname
  FROM emp e,(
      SELECT job,COUNT(*) count
      FROM emp
      GROUP BY job) empjobtemp,emp m,(
      SELECT job,COUNT(*) count
      FROM emp
      WHERE job IN (
          SELECT job
          FROM emp
          WHERE empno IN (
              SELECT DISTINCT mgr FROM emp))
      GROUP BY job) mgrjobtemp,dept d,(
      SELECT deptno dno,COUNT(*) count
      FROM emp
      GROUP BY deptno) empdepttemp,dept d2
  WHERE e.job=empjobtemp.job
      AND e.mgr=m.empno(+)
      AND m.job=mgrjobtemp.job
      AND e.deptno=d.deptno
      AND e.deptno=empdepttemp.dno
      AND m.deptno=d2.deptno;
  ```
  * **第八步：**查询出领导所在的部门人数
    * 要查询出领导所在的部门编号：
    ```sql
    SELECT deptno dno,COUNT(*) count
    FROM emp
    WHERE deptno IN (
        SELECT deptno
        FROM emp
        WHERE mgr IN (
            SELECT mgr FROM emp))
    GROUP BY deptno;
    ```
    * 将两个查询整合
    ```sql
    SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,
        m.ename,m.job,mgrjobtemp.count,d.dname,empdepttemp.count,
        d2.dname,mgrdepttemp.count
    FROM emp e,(
        SELECT job,COUNT(*) count
        FROM emp
        GROUP BY job) empjobtemp,emp m,(
        SELECT job,COUNT(*) count
        FROM emp
        WHERE job IN (
            SELECT job
            FROM emp
            WHERE empno IN (
                SELECT DISTINCT mgr FROM emp))
        GROUP BY job) mgrjobtemp,dept d,(
        SELECT deptno dno,COUNT(*) count
        FROM emp
        GROUP BY deptno) empdepttemp,dept d2,(
        SELECT deptno dno,COUNT(*) count
        FROM emp
        WHERE deptno IN (
            SELECT deptno
            FROM emp
            WHERE mgr IN (
                SELECT mgr FROM emp))
        GROUP BY deptno) mgrdepttemp
    WHERE e.job=empjobtemp.job
        AND e.mgr=m.empno(+)
        AND m.job=mgrjobtemp.job
        AND e.deptno=d.deptno
        AND e.deptno=empdepttemp.dno
        AND m.deptno=d2.deptno
        AND m.deptno=mgrdepttemp.dno;
    ```
  * **第九步：**雇员工资等级，直接引入salgrade表
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,
      m.ename,m.job,mgrjobtemp.count,d.dname,empdepttemp.count,
      d2.dname,mgrdepttemp.count,s.grade
  FROM emp e,(
      SELECT job,COUNT(*) count
      FROM emp
      GROUP BY job) empjobtemp,emp m,(
      SELECT job,COUNT(*) count
      FROM emp
      WHERE job IN (
          SELECT job
          FROM emp
          WHERE empno IN (
              SELECT DISTINCT mgr FROM emp))
      GROUP BY job) mgrjobtemp,dept d,(
      SELECT deptno dno,COUNT(*) count
      FROM emp
      GROUP BY deptno) empdepttemp,dept d2,(
      SELECT deptno dno,COUNT(*) count
      FROM emp
      WHERE deptno IN (
          SELECT deptno
          FROM emp
          WHERE mgr IN (
              SELECT mgr FROM emp))
      GROUP BY deptno) mgrdepttemp,salgrade s
  WHERE e.job=empjobtemp.job
      AND e.mgr=m.empno(+)
      AND m.job=mgrjobtemp.job
      AND e.deptno=d.deptno
      AND e.deptno=empdepttemp.dno
      AND m.deptno=d2.deptno
      AND m.deptno=mgrdepttemp.dno
      AND e.sal BETWEEN s.losal AND s.hisal;
  ```
  * **第十步：**找到对应等级的人数，直接追加一个子查询即可
  ```sql
  SELECT e.empno,e.ename,e.job,e.sal,empjobtemp.count,
      m.ename,m.job,mgrjobtemp.count,d.dname,empdepttemp.count,
      d2.dname,mgrdepttemp.count,s.grade,empsaltamp.count
  FROM emp e,(
      SELECT job,COUNT(*) count
      FROM emp
      GROUP BY job) empjobtemp,emp m,(
      SELECT job,COUNT(*) count
      FROM emp
      WHERE job IN (
          SELECT job
          FROM emp
          WHERE empno IN (
              SELECT DISTINCT mgr FROM emp))
      GROUP BY job) mgrjobtemp,dept d,(
      SELECT deptno dno,COUNT(*) count
      FROM emp
      GROUP BY deptno) empdepttemp,dept d2,(
      SELECT deptno dno,COUNT(*) count
      FROM emp
      WHERE deptno IN (
          SELECT deptno
          FROM emp
          WHERE mgr IN (
              SELECT mgr FROM emp))
      GROUP BY deptno) mgrdepttemp,salgrade s,(
      SELECT s1.grade grd,COUNT(e1.empno) count
      FROM emp e1,salgrade s1
      WHERE e1.sal BETWEEN s1.losal AND s1.hisal
      GROUP BY s1.grade) empsaltamp
  WHERE e.job=empjobtemp.job
      AND e.mgr=m.empno(+)
      AND m.job=mgrjobtemp.job
      AND e.deptno=d.deptno
      AND e.deptno=empdepttemp.dno
      AND m.deptno=d2.deptno
      AND m.deptno=mgrdepttemp.dno
      AND e.sal BETWEEN s.losal AND s.hisal
      AND s.grade=empsaltamp.grd;
  ```