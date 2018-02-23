# Java数据库编程（JDBC）

---

## JDBC简介

在Java语言设计的时候除了考虑到平台的编程技术之外，为了方便用户的进行各种情况下的开发还提供有一系列的服务，而数据库的操作就属于Java的服务范畴。服务的最大特点：所有的操作部分几乎都是固定的流程，也就是说服务几乎没有技术含量，属于应用，而对于所有的应用，代码的流程是固定的。

JDBC（Java Database Connective），Java数据库连接技术，即：是由Java提供的一组与平台无关的数据库的操作标准（是一组接口的组成），由于数据库属于资源操作，所以所有的数据库操作的最后必须要关闭数据库连接。

在JDBC技术范畴里面实际上规定了四种Java数据库操作的形式：

* 形式一：JDBC-ODBC桥接技术（100%不用）
  * 在Windows中有ODBC技术，ODBC指的是开放数据库连接，是由微软提供的数据库连接应用，而Java可以利用JDBC间接操作ODBC技术，从而实现数据库的链接；
  * 流程：程序 → JDBC → ODBC → 数据库，性能是最差的，支持的版本是最新的；
* 形式二：JDBC直接连接
  * 直接由不同的数据库生产商提供指定的数据库连接驱动程序（实现了Java的数据库操作标准的一群类），此类方式由于是JDBC直接访问数据库，所以性能是最好的。支持的JDBC版本不是最新的；
* **形式三：JDBC网络连接**
  * 使用专门的数据库的网络连接指令进行指定主机的数据库操作，此种方式使用最多；
* 形式四：模拟指定数据库的通讯协议自己编写数据库操作。

Java技术连接任何数据库性能都是很高的，除了SQL Server。

## 连接Oracle数据库

在Java之中，所有数据库操作的类和接口都保存在了java.sql包里面，在这个包里面核心的组成如下：

* 一个类：DriverManger类；
* 四个接口：Connection、Statement、ResultSet、PreparedStatment。

所有的JDBC连接数据库的操作流程都是固定的，按照如下的几步完成：

1. 加载数据库的驱动程序（向容器加载）；
2. 进行数据库连接（通过DriverManager类完成，Connection表示连接）；
3. 进行数据的CRUD（Statment、PreparedStatment、ResultSet）；
4. 关闭数据库操作以及连接（直接关闭连接就够了）。

**一、加载驱动程序**

所有的JDBC实际上都是有各个不同的数据库生产商提供的数据库驱动程序，这些驱动程序都是以*.jar文件的形式给出来的，所以如果要使用就需要为其配置CLASSPATH，而后要设置驱动程序的类名称（包.类）；

* 驱动程序：/app/Administrator/product/11.2.0/dbhome_1/jdbc/lib/ojdbc6.jar；
* Oracle驱动程序类：oracle.jdbc.driver.OracleDriver。
* 加载类使用Class.forName("oracle.jdbc.driver.OracleDriver")；

**二、连接数据库**

如果想要连接数据库需要提供有如下的几个信息（前提：数据库服务要打开）：

* 数据库的链接地址：jdbc:oracle:连接方式:@主机:端口:数据库SID
  * jdbc:oracle:thin:@localhost:1521:orcl；
* 数据库的用户名：scott；
* 数据库的密码:tiger；

要连接数据库必须依靠DriverManager类完成，在此类定义有如下的方法：

* 连接数据库：public static Connection getConnection(String url, String user, String password) throws SQLException；

在JDBC里面，每一个数据库连接都要求使用一个Connection对象进行封装，所以只要有一个新的Connection对象就表示要连接一次数据库。

**四、关闭数据库**

Connection接口提供有close()方法：public void close() throws SQLException；

**范例：**连接数据库

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
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
		System.out.println(conn);
		// 4.关闭数据库
		conn.close();
	}
}
```

此时的程序已经可以正常的了解Oracle数据库，可是很多时候是连接不上Oracle数据库的。

**1、监听服务出现错误**

* 监听的主机名称不是本机的计算机名称，也不要使用IP地址；
* 监听配置文件路径：/app/Administrator/product/11.2.0/dbhome_1/NETWORK/ADMIN，路径下面有两个文件：
  * 监听文件：listener.ora；
  * 监听名文件：tnsnames.ora；

**2、不能找到指定的SID**

数据库的名字就是SID的名字，但是很多时候会发现该名称不会自动注册，也就是说只有数据库名称，但是没有对应的SID名称。可以打开数据库的网络管理工具（开始 → Net Manager）。

通过以上的操作可以发现，整个数据库进行连接操作的时候都是按照同样的步骤进行的：

* DriverManager类取得Connection接口对象。

![](/images/chapter-3/section-20/1.png)

可以看出JDBC操作中，在驱动数据库连接对象是，采用的是工厂设计模式，而DriverManager就是一个工厂类，客户端调用的时候回完全隐藏具体的实现类。

## Statement接口

当取得了数据库连接对象之后，那么实际上就意味着可以进行数据库操作了，而数据库操作可以使用最简单的Statement接口完成。

如果要想取得Statement接口的实例化对象则需要依靠Connection接口提供的方法完成：

* 取得Statement接口对象：public Statement createStatement() throws SQLException；

当取得了Statement接口对象之后可以使用以下的两个方法实现数据库操作：

* 数据更新：public int executeUpdate(String sql) throws SQLException，返回更新行数；
* 数据查询：public ResultSet executeQuery(String sql) throws SQLException。

**范例：**编写数据库创建脚本

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

**范例：**数据增加

* 语法：INSERT INTO 表名称(列, 列, ...) VALUES(值, 值, ...);

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;
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

**范例：**数据修改

* 语法：UPDATE 表名称 SET 字段 = 值, ... WHERE 更新条件(s);

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;
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

**范例：**删除数据

* 语法：DELETE FROM 表名称 WHERE 删除条件(s);

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;
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

![](/images/chapter-3/section-20/2.png)

在ResultSer接口里面定义了如下的方法：

* 向下移动指针并判断是否有数据行：public boolean next() throws SQLException;
  * 移动之后就可以直接取得当前数据行中所有数据列的内容；
* 取出数据列的内容：getInt()、getDouble()、getString()、getDate()。

**范例：**实现数据的查询

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.Date;
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

关于ResultSet的使用有如下几点需要注意：

* 在使用getXxx()取出列数据的时候，强烈建议按照给定的顺序取；
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

## PreparedStatment接口

### Statement接口问题

虽然在JDBC里面提供有Statment接口，但是从实际来讲，Statement接口存在有严重的操作缺陷，是不可能在实际的工作之中使用的。

**范例：**以数据增加操作为例观察Statement的问题

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;
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

Statement如果要想变为灵活的应用，那么就必须采用拼凑字符串的方式完成，可是如果输入的内容存在有“'”，那么整个SQL就会出错，也就是说Statement的执行模式不适合于处理一些敏感字符。

### PreparedStatment操作

Statement执行的关键性的问题是在于它需要一个完整的字符串来定义要使用的SQL语句，所以这就导致在使用中就需要进行大量的SQL拼凑，而PreparedStatment与Statement不同的地方在于，它执行的是一个完整的具备特殊占位标记的SQL语句，并且可以动态的设置所需要的数据。

PreparedStatment属于Statement的子接口，但是如果要想取得这个子接口的实例化对象，依然需要使用Connection接口所提供的方法：public PreparedStatement prepareStatement(String sql) throws SQLException，在执行的时候需要传入一个SQL语句，这个SQL是一个具备特殊标记的完整SQL，但是此时没有内容，当取得了PreparedStatment接口对象后，需要使用一系列的setXxx()方法用于为所使用的标记设置具体内容，而后对于执行操作有两个方法支持：

* 更新操作：public int executeUpdate() throws SQLException；
* 查询操作：public ResultSet executeQuery() throws SQLException；

当使用了PreparedStatment接口操作时最需要注意的是里面的setDate()方法，因为此方法使用的是java.sql.Date而不是java.util.Date。

在java.util.Date类下面有三个子类都是在java.sql包中的：

* java.sql.Date：描述的是日期；
* java.sql.Time：描述的是时间；
* java.sql.Timestamp：描述的是时间戳（日期时间）；

如果要将java.util.Date变为java.sql.Date（Time、Timestamp）只能够依靠long完成：

* java.util.Date：public long getTime()，可以将Date变为long；
* java.sql.Date：public Date(long date)，将long变为java.sql.Date；

**范例：**改进数据增加

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.util.Date;
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

**范例：**查询全部数据

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.Date;
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

**范例：**模糊查询

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.Date;
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

**范例：**分页显示

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.Date;
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

**范例：**统计数据量，使用COUNT()函数

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
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

以上所讲解的所有PreparedStatment接口的操作为核心功能。

## 批处理与事务处理

在之前所使用的全部的数据库操作严格来讲是属于JDBC1.0中就规定的操作模式，而最新的JDBC是4.0版本，但是没人去使用，从JDBC2.0开始增加了一些神奇功能：可滚动的结果集、可以利用结果集执行增加、更新、删除操作、批处理操作。

所谓的批处理指的是一次性向数据库中发出多条操作命令，一起执行。如果要想操作批处理主要还是在Statment与PreparedStatment接口上定义的。

* Statement接口定义的方法：
  * 增加批处理：public void addBatch(String sql) throws SQLException；
  * 执行批处理：public int[] executeBatch() throws SQLException；
    * 返回的数组是包含了所有批处理语句的执行结果；
* PreparedStatment接口定义的方法：
  * 增加批处理：public void addBatch() throws SQLException；

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;
import java.util.Arrays;
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		Class.forName(DBDRIVER);
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		Statement stmt = conn.createStatement();
		stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试A')");
		stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试B')");
		stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试C')");
		stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试D')");
		stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试E')");
		stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试F')");
		int[] result = stmt.executeBatch(); // 执行批处理
		System.out.println(Arrays.toString(result));
		conn.close();
	}
}
```

如果假设以上的五条批处理属于一组关联的操作，如果中间有一条语句执行失败，其他的不应该成功。

在批处理操作的过程之中，由于JDBC具备有自动的事务提交，所以一旦中间的语句出现了错误，那么结果就是错误前的语句正常执行，错误后的语句就不执行了，很明显这不应该。

可以使用JDBC提供的事务处理操作来进行手工的事务控制，所有的操作方法都在Connection接口里定义：

* 事务提交：public void commit() throws SQLException；
* 事务回滚：public void rollback() throws SQLException；
* 设置是否为自动提交：public void setAutoCommit(boolean autoCommit) throws SQLException；

**范例：**利用事务处理

```java
package com.alpha.demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;
import java.util.Arrays;
public class TestDemo {
	private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver";
	private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:orcl";
	private static final String USER = "scott";
	private static final String PASSWORD = "tiger";
	public static void main(String[] args) throws Exception {
		Class.forName(DBDRIVER);
		Connection conn = DriverManager.getConnection(DBURL, USER, PASSWORD);
		Statement stmt = conn.createStatement();
		conn.setAutoCommit(false);
		try {
			stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试A')");
			stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试B')");
			stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试C')");
			stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试D')");
			stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试E')");
			stmt.addBatch("INSERT INTO member(mid,name) VALUES(myseq.nextval,'测试F')");
			int[] result = stmt.executeBatch(); // 执行批处理
			System.out.println(Arrays.toString(result));
			conn.commit(); // 如果没有错误进行提交
		} catch (Exception e) {
			e.printStackTrace();
			conn.rollback(); // 如果出现异常则进行回滚
		}
		conn.close();
	}
}
```

```java
```

