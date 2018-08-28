## Statement接口

当取得了数据库连接对象之后，那么实际上就意味着可以进行数据库操作了，而数据库操作可以使用最简单的`Statement`接口完成。

如果要想取得`Statement`接口的实例化对象则需要依靠`Connection`接口提供的方法完成：

* 取得`Statement`接口对象：`public Statement createStatement() throws SQLException`；

当取得了`Statement`接口对象之后可以使用以下的两个方法实现数据库操作：

* 数据更新：`public int executeUpdate(String sql) throws SQLException`，返回更新行数；
* 数据查询：`public ResultSet executeQuery(String sql) throws SQLException`。

**范例：** 编写数据库创建脚本
```sql
DROP TABLE member PURGE;
DROP SEQUENCE myseq;
CREATE SEQUENCE myseq;
CREATE TABLE member(
	mid		NUMBER,
	name		VARCHAR2(20),
	birthday	DATE	DEFAULT SYSDATE,
	age		NUMBER(3),
	note		CLOB,
	CONSTRAINT pk_mid PRIMARY KEY(mid)
);
```

mid要通过序列生成，序列操作“nextval”伪列取得下一个增长的数值。

###数据更新操作

数据的更新操作最关键的问题是每次完成更新之后都一定对返回影响的数据行数。

**范例：** 数据增加

* 语法：`INSERT INTO 表名称(列, 列, ...) VALUES(值, 值, ...)`;

```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 3.进行数据库的数据操作
		Statement stmt = conn.createStatement();
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "INSERT INTO member(mid, name, birthday, age, note) VALUES "
				+ " (myseq.nextval, '张三', TO_DATE('1998-10-10','yyyy-mm-dd'), 21, '是个人')";
		int len = stmt.executeUpdate(sql); // 执行SQL返回更新的数据行
		System.out.println("影响的数据行：" + len);
		// 4.关闭数据库
		conn.close();
	}
}
```

**范例：** 数据修改

* 语法：`UPDATE 表名称 SET 字段 = 值, ... WHERE 更新条件(s);`

```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 3.进行数据库的数据操作
		Statement stmt = conn.createStatement();
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "UPDATE member SET name = '李四', birthday = SYSDATE, age = 30 WHERE mid IN(3, 5, 7, 9, 11, 13, 15, 17)";
		int len = stmt.executeUpdate(sql); // 执行SQL返回更新的数据行
		System.out.println("影响的数据行：" + len);
		// 4.关闭数据库
		conn.close();
	}
}
```

**范例：** 删除数据

* 语法：`DELETE FROM 表名称 WHERE 删除条件(s);`

```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 3.进行数据库的数据操作
		Statement stmt = conn.createStatement();
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "DELETE FROM member WHERE mid IN(10, 20, 30)";
		int len = stmt.executeUpdate(sql); // 执行SQL返回更新的数据行
		System.out.println("影响的数据行：" + len);
		// 4.关闭数据库
		conn.close();
	}
}
```

整个更新操作完成后会清楚的感受到，只是更换了一个字符串而已，没有任何的本质变化。

### 数据查询

每当使用SELECT进行查询的时候实际上会将所有的查询结果返回给用户显示，而现实的基本结构就是表的结构。可是如果要进行查询，这些查询的结果应该返回给程序，由程序来进行处理，那么就必须有一种类型可以接受所有的返回结果。在数据库里面虽然有可能有几百张数据表，但是整个数据表的组成数据类型都是固定的，所以在ResultSet设计的过程之中它是按照数据类型的方式来保存返回数据的。

![](/images/chapter-3/section-31/1.png)

在`ResultSer`接口里面定义了如下的方法：

* 向下移动指针并判断是否有数据行：`public boolean next() throws SQLException`;
  * 移动之后就可以直接取得当前数据行中所有数据列的内容；
* 取出数据列的内容：`getInt()`、`etDouble()`、`getString()`、`getDate()`。

**范例：** 实现数据的查询

```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 3.进行数据库的数据操作
		Statement stmt = conn.createStatement();
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "SELECT mid, name, age, birthday, note FROM member";
		ResultSet rs = stmt.executeQuery(sql);
		while (rs.next()) { // 循环取出返回的每一行数据
			int mid = rs.getInt("mid");
			String name = rs.getString("name");
			int age = rs.getInt("age");
			Date birthday = rs.getDate("birthday");
			String note = rs.getString("note");
			System.out.println(mid + "," + name + "," + age + "," + birthday + "," + note);
		}
		// 4.关闭数据库
		conn.close();
	}
}
```

关于`ResultSet`的使用有如下几点需要注意：

* 在使用`getXxx()`取出列数据的时候，强烈建议按照给定的顺序取；
* 每一个列的数据只能够按照顺序取一次；

以上的代码在取列内容的时候重复编写了列名称，这一点实际上可以忽略，因为在写SQL语句的时候就已经给出了列名称，在取出的时候可以按照序号取出。

```java
while (rs.next()) { // 循环取出返回的每一行数据
	int mid = rs.getInt(1);
	String name = rs.getString(2);
	int age = rs.getInt(3);
	Date birthday = rs.getDate(4);
	String note = rs.getString(5);
	System.out.println(mid + "," + name + "," + age + "," + birthday + "," + note);
}
```

既然可以执行查询，那么就可以继续添加各种复杂查询，例如：限定查询、分组查询、统计查询、子查询。
