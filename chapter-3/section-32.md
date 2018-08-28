## PreparedStatment接口

### Statement接口问题

虽然在JDBC里面提供有`Statment`接口，但是从实际来讲，`Statement`接口存在有严重的操作缺陷，是不可能在实际的工作之中使用的。

**范例：** 以数据增加操作为例观察`Statement`的问题
```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		String name = "Mr'SMITH";
		String birthday = "1998-10-10";
		int age = 18;
		String note = "是个歪果仁";
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 3.进行数据库的数据操作
		Statement stmt = conn.createStatement();
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "INSERT INTO member(mid, name, birthday, age, note) VALUES " + " (myseq.nextval, '" + name
				+ "', TO_DATE('" + birthday + "','yyyy-mm-dd'), " + age + ", '" + note + "')";
		System.out.println(sql);
		int len = stmt.executeUpdate(sql); // 执行SQL返回更新的数据行
		System.out.println("影响的数据行：" + len);
		// 4.关闭数据库
		conn.close();
	}
}
```

```java
INSERT INTO member(mid, name, birthday, age, note) VALUES  (myseq.nextval, 'Mr'SMITH', TO_DATE('1998-10-10','yyyy-mm-dd'), 18, '是个歪果仁')
Exception in thread "main" java.sql.SQLSyntaxErrorException: ORA-00917: 缺失逗号
```

`Statement`如果要想变为灵活的应用，那么就必须采用拼凑字符串的方式完成，可是如果输入的内容存在有“'”，那么整个SQL就会出错，也就是说`Statement`的执行模式不适合于处理一些敏感字符。

### PreparedStatment操作

`Statement`执行的关键性的问题是在于它需要一个完整的字符串来定义要使用的SQL语句，所以这就导致在使用中就需要进行大量的SQL拼凑，而`PreparedStatment`与`Statement`不同的地方在于，它执行的是一个完整的具备特殊占位标记的SQL语句，并且可以动态的设置所需要的数据。

`PreparedStatment`属于`Statement`的子接口，但是如果要想取得这个子接口的实例化对象，依然需要使用`Connection`接口所提供的方法：`public PreparedStatement prepareStatement(String sql) throws SQLException`，在执行的时候需要传入一个SQL语句，这个SQL是一个具备特殊标记的完整SQL，但是此时没有内容，当取得了`PreparedStatment`接口对象后，需要使用一系列的`setXxx()`方法用于为所使用的标记设置具体内容，而后对于执行操作有两个方法支持：

* 更新操作：`public int executeUpdate() throws SQLException`；
* 查询操作：`public ResultSet executeQuery() throws SQLException`；

当使用了`PreparedStatment`接口操作时最需要注意的是里面的`setDate()`方法，因为此方法使用的是`java.sql.Date`而不是`java.util.Date`。

在`java.util.Date`类下面有三个子类都是在`java.sql`包中的：

* `java.sql.Date`：描述的是日期；
* `java.sql.Time`：描述的是时间；
* `java.sql.Timestamp`：描述的是时间戳（日期时间）；

如果要将`java.util.Date`变为`java.sql.Date`（`Time`、`Timestamp`）只能够依靠`long`完成：

* `java.util.Date`：`public long getTime()`，可以将`Date`变为`long`；
* `java.sql.Date`：`public Date(long date)`，将`long`变为`java.sql.Date`；

**范例：** 改进数据增加
```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		String name = "Mr'SMITH";
		Date birthday = new Date();
		int age = 18;
		String note = "是个歪果仁";
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "INSERT INTO member(mid, name, birthday, age, note) VALUES(myseq.nextval,?,?,?,?)";
		// 3.进行数据库的数据操作，执行了完整的SQL
		PreparedStatement pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, name);
		pstmt.setDate(2, new java.sql.Date(birthday.getTime()));
		pstmt.setInt(3, age);
		pstmt.setString(4, note);
		int len = pstmt.executeUpdate(); // 执行SQL返回更新的数据行
		System.out.println("影响的数据行：" + len);
		// 4.关闭数据库
		conn.close();
	}
}
```

更新与删除操作和增加操作的区别不大。

整个数据库操作里面更新几乎都是固定的模式，但是查询操作是最为复杂的。

**范例：** 查询全部数据
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
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "SELECT mid,name,birthday,age,note FROM member ORDER BY mid";
		// 3.进行数据库的数据操作，执行了完整的SQL
		PreparedStatement pstmt = conn.prepareStatement(sql);
		ResultSet rs = pstmt.executeQuery();
		while (rs.next()) {
			int mid = rs.getInt(1);
			String name = rs.getString(2);
			Date birthday = rs.getDate(3);
			int age = rs.getInt(4);
			String note = rs.getString(5);
			System.out.println(mid + "," + name + "," + birthday + "," + age + "," + note);
		}
		// 4.关闭数据库
		conn.close();
	}
}
```

**范例：** 模糊查询
```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		String keyWord = "李";
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = "SELECT mid,name,birthday,age,note FROM member WHERE name LIKE ?";
		// 3.进行数据库的数据操作，执行了完整的SQL
		PreparedStatement pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, "%" + keyWord + "%");
		ResultSet rs = pstmt.executeQuery();
		while (rs.next()) {
			int mid = rs.getInt(1);
			String name = rs.getString(2);
			Date birthday = rs.getDate(3);
			int age = rs.getInt(4);
			String note = rs.getString(5);
			System.out.println(mid + "," + name + "," + birthday + "," + age + "," + note);
		}
		// 4.关闭数据库
		conn.close();
	}
}
```

**范例：** 分页显示
```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		int currentPage = 1;
		int lineSize = 5;
		String keyWord = "李";
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = " SELECT * FROM ("
				+ " SELECT mid,name,birthday,age,note,ROWNUM rn "
				+ " FROM member "
				+ " WHERE name LIKE ? AND ROWNUM <= ?) temp "
				+ " WHERE temp.rn > ?";
		// 3.进行数据库的数据操作，执行了完整的SQL
		PreparedStatement pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, "%" + keyWord + "%");
		pstmt.setInt(2, currentPage * lineSize);
		pstmt.setInt(3, (currentPage -1) * lineSize);
		ResultSet rs = pstmt.executeQuery();
		while (rs.next()) {
			int mid = rs.getInt(1);
			String name = rs.getString(2);
			Date birthday = rs.getDate(3);
			int age = rs.getInt(4);
			String note = rs.getString(5);
			System.out.println(mid + "," + name + "," + birthday + "," + age + "," + note);
		}
		// 4.关闭数据库
		conn.close();
	}
}
```

**范例：** 统计数据量，使用`COUNT()`函数
```java
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		String keyWord = "李";
		// 1.加载数据库驱动程序，此时不需要实例化，因为会由容器自己负责管理
		Class.forName(DBDRIVER);
		// 2.连接数据库
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		// 在编写SQL的过程里面，如果太长的时候需要增加换行，一定要加上前后空格
		String sql = " SELECT COUNT(mid) FROM member WHERE name LIKE ?";
		// 3.进行数据库的数据操作，执行了完整的SQL
		PreparedStatement pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, "%" + keyWord + "%");
		ResultSet rs = pstmt.executeQuery();
		if (rs.next()) { // 只有一行数据
			int count = rs.getInt(1);
			System.out.println(count);
		}
		// 4.关闭数据库
		conn.close();
	}
}
```

以上所讲解的所有`PreparedStatment`接口的操作为核心功能。
