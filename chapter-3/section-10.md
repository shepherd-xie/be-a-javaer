## 反射机制

---

* 认识反射；
* 理解反射的作用；
* 利用反射来调用类的结构。

### 认识反射

在正常的情况下，一定是先有类而后再有产生对象。

```java
package com.alpha;
import java.util.Date; // 先有类
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Date date = new Date(); // 产生对象
		System.out.println(date);
	}
}
```

所谓反射就是可以利用对象找到对象的出处，在Object类里面提供有一个方法：

* 取得Class对象：public final Class<?> getClass()

**范例：** 观察反射
```java
package com.alpha;
import java.util.Date; // 先有类
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Date date = new Date(); // 产生对象
		System.out.println(date.getClass());
	}
}
```

```
class java.util.Date
```

发现调用了getClass()方法后的输出就输出了类的完整名称，等于找到了对象的出处。

### Class类对象实例化

java.util.Class是一个类，这个类是反射操作的源头，即：所有的反射都要从此类开始进行，而最关键的是这个类有三种实例化方式：

* 第一种：调用Object类中的getClass()方法；
```java
package com.alpha;
import java.util.Date; // 先有类
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Date date = new Date(); // 产生对象
		Class<?> cls = date.getClass();
		System.out.println(cls);
	}
}
```

* 第二种：使用“类.class”取得；
```java
package com.alpha;
import java.util.Date; // 先有类
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Class<?> cls = Date.class;
		System.out.println(cls);
	}
}
```

之前是在产生了类的实例化对象之后取得的Class类对象，但是此时并没有实例化对象产生。

* 第三种：调用Class类提供的一个方法；
  * 实例化Class类对象：public static Class<?> forName(String className) throws ClassNotFoundException

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Class<?> cls = Class.forName("java.util.Date");
		System.out.println(cls);
	}
}
```

此时可以不使用import语句导入一个明确的类，而类名称是采用字符串的形式进行描述的。

### 反射实例化对象

当拿到一个类的时候，肯定要直接使用关键字new进行对象的实例化操作，这属于习惯性的做法，但是如果有了Class类对象，那么就可以做到，利用反射来实现对象实例化操作：

* 实例化对象方法：public T newInstance() throws InstantiationException, IllegalAccessException

**范例：** 利用反射实例化对象
```java
package com.alpha;
class Book {
	public Book() {
		System.out.println("Book without parameters of the construction method.");
	}
	@Override
	public String toString() {
		return "This is a book.";
	}
}
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Class<?> cls = Class.forName("com.alpha.Book");
		Object obj = cls.newInstance(); // 相当于使用new调用无参构造实例化
		System.out.println(obj);
	}
}
```

有了反射之后，以后进行对象的实例化的操作不在只是单独的依靠关键字new完成，反射也同样可以完成，但是这并不表示new就被完全取代了。

在任何的开发之中，new是造成耦合的最大元凶，一切的耦合都起源于new。

**范例：** 观察工厂设计模式
```java
package com.alpha.factory;
interface Fruit {
	public void eat();
}
class Apple implements Fruit {
	@Override
	public void eat() {
		System.out.println("eat apple");
	}
}
class Factory {
	public static Fruit getInstance(String className) {
		if ("apple".equals(className)) {
			return new Apple();
		}
		return null;
	}
}
public class TestFactory {
	public static void main(String[] args) {
		Fruit f = Factory.getInstance("apple");
		f.eat();
	}
}
```

如果此时增加了Fruit接口子类，那么就表示程序要修改工厂类。

```java
class Orange implements Fruit {
	@Override
	public void eat() {
		System.out.println("eat orange");
	}
}
class Factory {
	public static Fruit getInstance(String className) {
		if ("apple".equals(className)) {
			return new Apple();
		} else if ("orange".endsWith(className)) {
			return new Orange();
		}
		return null;
	}
}
```

每增加一个类就要去修改工厂类，那么如果随时都可能增加子类呢？工厂类要被一直进行修改。

因为现在工厂类中的对象都是通过关键字new直接实例化的，而new就成了所有问题的关键点。要想解决这个问题就要依靠反射完成。

```java
package com.alpha.factory;
interface Fruit {
	public void eat();
}
class Apple implements Fruit {
	@Override
	public void eat() {
		System.out.println("eat apple");
	}
}
class Orange implements Fruit {
	@Override
	public void eat() {
		System.out.println("eat orange");
	}
}
class Factory {
	public static Fruit getInstance(String className) {
		Fruit f = null;
		try {
			f = (Fruit) Class.forName(className).newInstance();
		} catch (Exception e) {
			e.printStackTrace();
		}
		return f;
	}
}
public class TestFactory {
	public static void main(String[] args) {
		Fruit f = Factory.getInstance("com.alpha.factory.Apple");
		f.eat();
	}
}
```

此时的程序就真正完成了解耦合的作用，而且增强了程序的可拓展性。

### 使用反射调用构造

在之前所编写的代码实际上都默认使用了类中的无参构造方法，但是并不是所有的类都提供有无参构造。

**范例：** 观察程序的问题
```java
package com.alpha.entity;
public class Book {
	private String title;
	private double price;
	public Book(String title, double price) {
		this.title = title;
		this.price = price;
	}
	@Override
	public String toString() {
		return "Book [title=" + title + ", price=" + price + "]";
	}
}
```

由于此时Book类没有提供无参构造方法，所以以下的代码是错误的。

```java
package com.alpha;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Class<?> cls = Class.forName("com.alpha.entity.Book");
		Object obj = cls.newInstance(); // 相当于使用new调用无参构造实例化
		System.out.println(obj);
	}
}
```

```java
Exception in thread "main" java.lang.InstantiationException: com.alpha.entity.Book
	at java.lang.Class.newInstance(Unknown Source)
	at com.alpha.MainClass.main(MainClass.java:6)
Caused by: java.lang.NoSuchMethodException: com.alpha.entity.Book.<init>()
	at java.lang.Class.getConstructor0(Unknown Source)
	... 2 more
```

以上所出现的错误指的就是因为当前Book类里面并没有无参构造方法，所以程序无法进行对象的实例化。在这种情况下，只能够明确的调用有参构造方法。

在Class类里面提供有一个方法可以取得构造：

* 取得全部构造：public Constructor<?>[] getConstructors() throws SecurityException
* 取得一个指定参数的构造：public Constructor<T> getConstructor(Class<?>... parameterTypes) throws NoSuchMethodException, SecurityException

以上的方法返回的都是"java.lang.reflect.Constructor"类的对象。在这个类中提供有一个明确传递有参构造内容的实例化方法：public T newInstance(Object... initargs) throws InstantiationException, IllegalAccessException, IllegalArgumentException, InvocationTargetException。

**范例：** 明确调用类中有参构造

```java
package com.alpha;
import java.lang.reflect.Constructor;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Class<?> cls = Class.forName("com.alpha.entity.Book");
		Constructor<?> con = cls.getConstructor(String.class, double.class);
		Object obj = con.newInstance("java", 15.5);
		System.out.println(obj);
	}
}
```

简单Java类的开发之中不管提供有多少个构造方法，请至少保留有无参构造。

### 反射调用方法

**类的普通方法只有在一个类产生实例化对象之后才可以调用。并且实例化对象的方式有三种。**

**范例：** 定义一个类

```java
package com.alpha.entity;
public class Book {
	private String title;
	public void setTitle(String title) {
		this.title = title;
	}
	public String getTitle() {
		return title;
	}
}
```

这个类有无参构造方法，所以实例化对象的时候直接利用Class类中提供的newInstance()方法完成。

在Class类里面提供了一下取得类中Method的操作：

* 取得一个类中的全部方法：public Method[] getMethods() throws SecurityException
* 取得指定方法：public Method getMethod(String name, Class<?>... parameterTypes) throws NoSuchMethodException, SecurityException

以上两个操作返回的是"java.lang.reflect.Method"类的对象，在这个类里面重点关注一个方法。

* 调用方法：public Object invoke(Object obj, Object... args) throws IllegalAccessException, IllegalArgumentException, InvocationTargetException

**范例：** 反射调用方法

```java
package com.alpha;
import java.lang.reflect.Method;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String fieldName = "title";
		Class<?> cls = Class.forName("com.alpha.entity.Book");
		Object obj = cls.newInstance();
		Method setMet = cls.getMethod("set" + initcap(fieldName), String.class);
		Method getMet = cls.getMethod("get" + initcap(fieldName));
		setMet.invoke(obj, "java");
		System.out.println(getMet.invoke(obj));
	}
	public static String initcap(String str) {
		return str.substring(0, 1).toUpperCase() + str.substring(1);
	}
}
```

此时完全看不见具体的操作类型，也就是说利用反射可以实现任意类的指定方法的调用。

### 反射调用成员

类中的属性一定要在本类实例化对象产生之后才可以分配内存空间。在Class类里面提供有取得成员的方法：

* 取得全部成员：public Field[] getDeclaredFields() throws SecurityException
* 取得指定成员：public Field getDeclaredField(String name) throws NoSuchFieldException, SecurityException

返回的类型是"java.util.reflect.Field"类，这个类里面有两个重要的方法：

* 取得属性内容：public Object get(Object obj) throws IllegalArgumentException, IllegalAccessException
* 设置属性内容：public void set(Object obj, Object value) throws IllegalArgumentException, IllegalAccessException

在"java.lang.reflect.AccessibleObject"类下面（JDK1.8之后修改）：

* Executable：下面继续继承了Constructor、Method；
* Field：

在这个类里面提供有一个方法：public void setAccessible(boolean flag) throws SecurityException，设置是否封装。

**范例：** 现提供有如下的类

```java
package com.alpha.entity;
public class Book {
	private String title;
}
```

这个类里面只定义了一个私有属性，按照原始的做法，此时它一定无法被外部所使用。

**范例：** 反射调用

```java
package com.alpha;
import java.lang.reflect.Field;
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		String fieldName = "title";
		Class<?> cls = Class.forName("com.alpha.entity.Book");
		Object obj = cls.newInstance();
		Field titleField = cls.getDeclaredField(fieldName);
		titleField.setAccessible(true); // 取消封装
		titleField.set(obj, "java");
		System.out.println(titleField.get(obj));
	}
}
```

构造方法与普通方法也同样可以取消封装，只不过很少这样去做，而且对于属性的访问还是建议使用setter、getter方法完成。

#### 总结

1. 实例化对象的方式增加了一种反射；
2. 反射只是一个开始。


