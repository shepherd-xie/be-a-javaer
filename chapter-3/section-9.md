## 正则表达式

* [常用的正则标记](/chapter-3/section-9.md#常用的正则标记)
* [String类对正则的支持](/chapter-3/section-9.md#String类对正则的支持)
* [java.util.regex包支持](/chapter-3/section-9.md#java.util.regex包支持)

### 问题引出

下面要求编写一个程序：判断一个字符串是否由数字所组成。

实现原理：

* 将字符串变为字符数组；
* 而后判断每一个字符是否在'0'~'9'的范围之间。

**范例：**实现字符串的判断

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "1234a56879";
		System.out.println(isNumber(str));
	}
	public static boolean isNumber(String temp) {
		char[] data = temp.toCharArray(); // 将字符串变为字符数，目的是循环取出
		for (int i = 0; i < data.length; i ++) {
			if (data[i] > '9' || data[i] < '0') {
				return false;
			}
		}
		return true;
	}
}
```

此时判断字符串是否由数字所组成，是一个很容易的实现的功能，但是这样一个简短的操作，却写出了这么多代码，如果说需要更加复杂的判断，这种方式显然就不再适用了。

**范例：**更简单的做法

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "123456879";
		System.out.println(str.matches("\\d+"));
	}
}
```

对于之前的判断这里只用了一个字符串就实现了，而其中的"\\\\d+"就是正则表达式。

正则是从JDK1.4的时候正式引入到Java中的工具类，所有正则支持类都定义在java.util.regex包里面。在JDK1.4之前，如果想要使用正则，则需要单独下载一个正则表达式的开发包后才可以使用。

在java.util.regex包里面定义了两个主要的类：

* Pattern类：此类对象如果要想取得必须使用compile()方法，方法的功能是编译正则；
* Matcher类：通过Pattern类取得。

### 常用的正则标记

所有的正则可以使用的标记都在java.util.Pattern类里面定义。

1、单个字符

* x ：表示由任意一位字符所组成；
* \\\\：表示转义字符"\\"；
* \\t：表示制表符；
* \\n：表示换行符；

2、字符集

* \[abc\]：表示字符abc中任意一个；
* \[^abc\]：表示不是字符abc中任意一个；
* \[a-z\]：表示任意一位小写字母；
* \[a-zA-Z\]：表示任意一位字母；
* \[0-9\]：表示任意的一位数字；

3、简化的字符集

* . ：表示任意一位字符；
* \\d：表示\[0-9\]；
* \\D：表示\[^0-9\]；
* \\s：表示任意的空白字符，"\\t"、"\\n"；
* \\S：表示任意非空白字符；
* \\w：等价于\[a-zA-Z\_0-9\]，表示由任意的字母、数字、\_所组成；
* \\W：等价于\[^a-zA-Z\_0-9\]，表示不是由任意的字母、数字、\_所组成；

4、边界匹配

* ^ ：正则的开始；
* $ ：正则的结束；

5、数量表达

* X?：表示此正则可以出现0次或1次；
* X+：表示此正则可以出现1次或1次以上；
* X*：表示此正则可以出现0次1次或多次；
* X{n}：表示此正则出现n次；
* X{n,}：表示此正则至少出现n次；
* X{n, m}：表示此正则出现（包含）n到m次；

6、逻辑运算

* XY：正则X判断后再判断正则Y；
* X|Y：满足正则X或正则Y；
* (X)：将多个正则设置为一组；

### String类对正则的支持

在JDK1.4之后，由于正则的引入，所以String类里面也相应的增加了新的操作方法支持。

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public boolean matches(String regex) | 普通 | 正则验证，使用指定的字符串判断其是否符合给出的正则表达式 |
| 2 | public String replaceAll(String regex, String replacement) | 普通 | 全部替换 |
| 3 | public String replaceFirst(String regex, String replacement) | 普通 | 替换首个 |
| 4 | public String[] split(String regex) | 普通 | 全部拆分 |
| 5 | public String[] split(String regex, int limit) | 普通 | 部分拆分 |

给出的几个方法里面对于替换和拆分实际上难度不高，最关键的就是正则匹配，在验证上使用的特别多。

**范例：**实现字符串替换
```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "asdJHGJhbvK()*&809^9865&^TDiuSY*&tybdYvkHGuvLv";
		String regex = "[^a-z]"; // 此处编写正则
		System.out.println(str.replaceAll(regex, ""));
	}
}
```

**范例：**字符串拆分
```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "as15dJH453GJhbv43KTDiuSY3tyb43YvkHGu98vLv";
		String regex = "\\d+";
		String[] result = str.split(regex);
		for (String s : result) {
			System.out.println(s);
		}
	}
}
```

**范例：**验证一个字符串是否是数字，如果是则将其变为double型

* 数字可能是整数，也可能是小数；

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "10.1";
		String regex = "\\d+(\\.\\d+)?";
		System.out.println(str.matches(regex));
		if (str.matches(regex)) {
			System.out.println(Double.parseDouble(str));
		}
	}
}
```

**范例：**判断给定的字符串是否是一个IP地址（IPv4）

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "192.168.1.1";
		String regex = "\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}";
//		String regex = "(\\d{1,3}\\.){3}\\d{1,3}";
		System.out.println(str.matches(regex));
	}
}
```

**范例：**给定一个字符串，要求判断其是否是日期格式，如果是则将其转换为Date型数据

```java
package com.alpha;
import java.text.SimpleDateFormat;
import java.util.Date;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "2009-12-25";
		String regex = "\\d{4}-\\d{2}-\\d{2}";
		System.out.println(str.matches(regex));
		if (str.matches(regex)) {
			Date date = new SimpleDateFormat("yyyy-MM-dd").parse(str);
			System.out.println(date);
		}
	}
}
```

**范例：**判断电话号码

* 格式一：12345678，"\\\\d{7,8}"；
* 格式二：029-12345678，"(\\\\d{3,4}-)?\\\\d{7,8}"；
* 格式三：(029)-12345678，"((\\\\d{3,4}-)|(\\\\(\\\\d{3,4}\\\\)-))?\\\\d{7,8}"。

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "(029)-12345678";
		String regex = "((\\d{3,4}-)|(\\(\\d{3,4}\\)-))?\\d{7,8}";
		System.out.println(str.matches(regex));
	}
}
```

**范例：**验证email地址

* 要求格式一：email由字幕、数字、\_所组成。

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "hello_nihao123@git.com";
		String regex = "\\w+@\\w+\\.\\w+";
		System.out.println(str.matches(regex));
	}
}
```

* 要求格式二：用户名要求由字母、数字、\_、.组成，其中必须以字母开头，以字母或数字结尾，长度不超过30，最后的根域名只能够是.com、.cn、.net、.com.cn、.edu、.gov、.org。

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "hello_ni.hao123@git.com";
		String regex = "[a-zA-Z][a-zA-Z0-9_\\.]{0,28}[a-zA-Z0-9]@\\w+\\.(net|cn|com|com\\.cn|net|edu|org|gov)";
		System.out.println(str.matches(regex));
	}
}
```

### java.util.regex包支持

在大多数情况下使用正则的时候都会采用String类完成，但是正则最原始的开发包是java.util.regex，这个包里提供有两个类。

**范例：**Pattern类
```java
package com.alpha;
import java.util.Arrays;
import java.util.regex.Pattern;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "a1b22c333d4444e55555f";
		String regex = "\\d+";
		Pattern pattern = Pattern.compile(regex); // 编译正则
		String[] result = pattern.split(str); // 拆分字符串
		System.out.println(Arrays.toString(result));
	}
}
```

**范例：**字符串验证
```java
package com.alpha;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String str = "32323";
		String regex = "\\d+";
		Pattern pattern = Pattern.compile(regex); // 编译正则
		Matcher mat = pattern.matcher(str); // 进行正则匹配
		System.out.println(mat.matches()); // 匹配结果
	}
}
```

正是因为String类本身就已经支持这样两种操作了，所以很少再去利用这两个类进行操作。

#### 总结

1. 利用正则实现验证代码可以最少化；
2. 一定要清楚String类对正则支持的几个方法，以及所有讲解过的相关程序。

