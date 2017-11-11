## 国际化程序实现

---

在工作开发里面国际化应用一定会存在，只不过如果你只是针对使用情况下开发，国际化基本上就是应用。

如果说现在有一套程序，可能在多个国家使用。程序的功能不会被改变，唯一可能改变的就是语言。

如果要想实现语言的统一，那么唯一能够做的方式就是将所有需要显示的语言定义在各自的资源文件里。

所谓的国际化应用就是指根据当前的语言环境读取指定的语言资源文件。

如果要想实现国际化的操作，那么首先要解决的问题就是如何读取资源文件的问题。

所谓的资源文件指的是后缀名称为“*.properties”里面保存的内容按照“key=value”的形式保存，而且资源文件的命名标准与Java类完全一样。

**范例：**定义一个Message.porperties

* 如果保存的是中文信息，那么必须将其变为UNICODE编码

```java
info=\u4E2D\u534E\u4EBA\u6C11\u5171\u548C\u56FD
info=中华人民共和国
```

这里面保存的info是这个信息的key以后要根据这个key取得得对应的value。

如果要读取资源文件的信息使用java.util.ResourceBundle类，这是一个抽象类，但是这个类的内部也提供有一个static方法用于取得本类对象：

* 根据当前语言环境取出：public static final ResourceBundle getBundle(String baseName)；
* 设置指定语言环境：public static final ResourceBundle getBundle(String baseName, Locale locale)。

当取得了ResourceBunlde类对象之后可以通过以下的方法读取数据：

* 简单读取：public final String getString(String key)；

java.text是专门负责国际化处理的程序包，在这个包里面有一个专门处理占位数据的操作类：MessageFormat；

* 格式化文本：public static String format(String pattern, Object... arguments)。

**范例：**读取普通文本

```java
package com.alpha;
import java.util.ResourceBundle;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		// 访问的时候不要加后缀，因为默认找到的后缀就是“*.properties”
		// 此时的Message.properties文件一定要放在CLASSPATH路径下
		ResourceBundle rb = ResourceBundle.getBundle("Message");
		System.out.println(rb.getString("info"));
	}
}
```

很多时候数据是会被改变的，例如：

**范例：**修改Message.properties文件

```java
wel.msg=\u6B22\u8FCE{0}\u5149\u4E34\uFF0C\u73B0\u5728\u65F6\u95F4\u662F\uFF1A{1}\uFF01
wel.msg=欢迎{0}光临，现在时间是：{1}！
```

**范例：**设置读取的可变内容

```java
package com.alpha;
import java.text.MessageFormat;
import java.util.Date;
import java.util.ResourceBundle;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		// 访问的时候不要加后缀，因为默认找到的后缀就是“*.properties”
		// 此时的Message.properties文件一定要放在CLASSPATH路径下
		ResourceBundle rb = ResourceBundle.getBundle("Message");
		String str = rb.getString("wel.msg"); // 具有占位符的内容
		System.out.println(MessageFormat.format(str, "mori", new Date()));
	}
}
```
Locale保存的是一个国家的区域和语言编码：

* 中国：zh_CN；
* 美国：en_US；

可以在定义资源文件的时候加上指定的语言编码，例如

**范例：**定义中文的资源文件 —— Message_zh_CN.properties

```java
wel.msg=\u6362\u8FCE{0}\u5149\u4E34\uFF01
wel.msg=换迎{0}光临！
```

**范例：**定义英文的资源文件 —— Message_en_US.properties

```java
wel.msg=WELCOME {0}!
```

设置的baseName设置的一定是Message，所有的语言代码由Locale类设置，在Local类里面有以下方法：

* 创造方法：public Locale(String language, String country)；
* 取得当前语言环境：public static Locale getDefault()。

**范例：**读取中文文件

```java
package com.alpha;
import java.text.MessageFormat;
import java.util.Locale;
import java.util.ResourceBundle;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Locale loc = new Locale("zh", "CN");
		ResourceBundle rb = ResourceBundle.getBundle("Message", loc);
		String str = rb.getString("wel.msg"); // 具有占位符的内容
		System.out.println(MessageFormat.format(str, "mori"));
	}
}
```

**范例：**读取英文

```java
package com.alpha;
import java.text.MessageFormat;
import java.util.Locale;
import java.util.ResourceBundle;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Locale loc = new Locale("en", "US");
		ResourceBundle rb = ResourceBundle.getBundle("Message", loc);
		String str = rb.getString("wel.msg"); // 具有占位符的内容
		System.out.println(MessageFormat.format(str, "mori"));
	}
}
```

#### 总结

1、资源文件：文件名称每个单词首字母大写，后缀必须是“*.properties”；

2、通过ResourceBundle类可以读取在指定的CLASSPATH下的资源文件，读取时不需要加文件后缀；

  * 动态的占位文本格式化：MessageFormat。

2、Locale类用于指定读取的资源文件的语言环境。

