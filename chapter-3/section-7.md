## 日期处理类

---

* [Date类](/chapter-3/section-7.md#Date类)
* [日期格式化](/chapter-3/section-7.md#日期格式化)
* [Calendar类](/chapter-3/section-7.md#Calendar类)

### Date类

之前一直在编写简单Java类，但是所编写的数据表与简单Java类的转换里面缺少了Date数据类型，所以本部分就属于简单Java类的最后一块内容。

在Java里面提供有一个java.util.Date的类，它直接就表示当前的日期时间。

**范例：**取得当前的日期时间

```java
package com.alpha;
import java.util.Date;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Date date = new Date();
		System.out.println(date);
	}
}
```

这个时候的确是输出了当前的日期时间，只不过格式不是我们所习惯的类型（Sat Oct 21 15:57:32 CST 2017）。

一直以来强调过一个概念：long可以描述日期时间数据，那么这一点在Date类的方法上也是可以看见的，在Date类里面定义了如下的几个重要方法：

* 无参构造：public Date()；
* 有参构造：public Date(long date)；
* 转换为long型：public long getTime()。

**范例：**Date与long间的转换

```java
package com.alpha;
import java.util.Date;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		long cur = System.currentTimeMillis(); // 取得当前的日期时间 以long返回
		Date date = new Date(cur);
		System.out.println(date);
		System.out.println(date.getTime());
	}
}
```

以后的代码编写过程中，依然需要以上的转换操作，尤其是getTime()方法。

### 日期格式化

java.text是一个专门实现国际化程序的开发包，而SimpleDateFormat是一个专门处理格式化日期的工具类，即：可以将Date型的对象转化为String型的形式显示。而主要使用的是以下方法：

* 构造方法：public SimpleDateFormat(String pattern)，需要传递转换格式；
* 将Date转换为String：public final String format(Date date)；
* 将String转换为Date：public Date parse(String source) throws ParseException。

现在的关键就在于转换格式上，对于常见的转换单位：年（yyyy），月（MM），日（dd），时（HH），分（mm），秒（ss），毫秒（SSS）。

**范例：**将日期格式化（Date型数据变为了String型数据）

```java
package com.alpha;
import java.text.SimpleDateFormat;
import java.util.Date;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Date date = new Date();
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
		String str = sdf.format(date); // 将Date型变为String型
		System.out.println(str);
	}
}
```

除了可以将日期变为字符串之外，也可以将字符串转换为日期。

**范例：**将字符串转换为日期

```java
package com.alpha;
import java.text.SimpleDateFormat;
import java.util.Date;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "2011-11-11 11:11:11.111";
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
		Date date = sdf.parse(str); // 将字符串变为日期型
		System.out.println(date);
	}
}
```

在将字符串变为日期型数据的时候，如果日期型数据给出的数据超过日期的最大范围，那么会自动的进位。如果给定的字符串与要转换的格式不符合，那么就会出现异常。

_**总结：**关于数据表的转换_

_在数据表的操作里面重点说过了有几个常用类型：VARCHAR2（String）、CLOB（String）、Number（double、int）、Date（java.util.Date）。_

_Date与String类之间的转换依靠的是SimpleDateFormat；_

_String与基本类型之间的转换依靠的是包装类与String.valueOf()方法；_

_long与Date转换依靠的是Date类提供的构造以及getTime()方法。_

### Calendar类

Date类和SimpleDateFormat类两个往往是一起使用的，但是Calendar这个类主要是进行一些简单的日期计算。

Calendar类定义：

```java
public abstract class Calendar
extends Object
implements Serializable, Cloneable, Comparable<Calendar>
```

这是一个抽象类，那么应该依靠子类进行对象的实例化操作，但是在这个类里面他提供有一个static方法，此方法返回的正式本类对象：public static Calendar getInstance()。

**范例：**取得当前的日期时间

```java
package com.alpha;
import java.util.Calendar;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Calendar cale = Calendar.getInstance();
		StringBuffer sb = new StringBuffer();
		sb.append(cale.get(Calendar.YEAR)).append("-");
		sb.append(cale.get(Calendar.MONTH) + 1).append("-");
		sb.append(cale.get(Calendar.DAY_OF_MONTH)).append(" ");
		sb.append(cale.get(Calendar.HOUR_OF_DAY)).append(":");
		sb.append(cale.get(Calendar.MINUTE)).append(":");
		sb.append(cale.get(Calendar.SECOND));
		System.out.println(sb);
	}
}
```

这个类在取得信息的时候可以进行一些简单的计算，例如：若干天之后的日期；

```java
package com.alpha;
import java.util.Calendar;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Calendar cale = Calendar.getInstance();
		StringBuffer sb = new StringBuffer();
		sb.append(cale.get(Calendar.YEAR)).append("-");
		sb.append(cale.get(Calendar.MONTH) + 1).append("-");
		sb.append(cale.get(Calendar.DAY_OF_MONTH) + 3).append(" ");
		sb.append(cale.get(Calendar.HOUR_OF_DAY)).append(":");
		sb.append(cale.get(Calendar.MINUTE)).append(":");
		sb.append(cale.get(Calendar.SECOND));
		System.out.println(sb);
	}
}
```

#### 总结

1. 数据库中的日期型使用java.util.Date表示；
2. SimpleDateFormat类实现String与Date件的互相转换。

