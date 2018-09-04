## 数据流Stream

### Stream的基础操作

在JDK 1.8开始发现整个类集里面所提供的接口都出现了大量的`default`或者是`static`方法，以`Collection`的父借口`Iterable`接口里面定义的一个方法来观察：`default void forEach(Consumer<? super T> action)`。

**范例：** 利用`forEach()`方法输出
```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("Hello");
		all.add("World");
		all.add("Demo");
		// System.out.println(String str)
		all.forEach(System.out :: println);
	}
}
```

在日后的开发之中几乎不会使用到这种方式，因为`forEach()`只能够实现输出，但是很多时候我们需要在集合数据输出的同时还要进行数据的加工处理，主要使用的还是`Iterator`输出方式。

除了使用`Iterator`迭代取出数据并且处理之外，在JDK 1.8里面又提供了一个专门可以进行数据处理的类：`java.util.stream.Stream`，这个类的对象可以利用`Collection`接口提供的方法操作：`default Stream<E> stream()`。

**范例：** 取得`Stream`对象
```java
package com.alpha.demo;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("Hello");
		all.add("World");
		all.add("Demo");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		System.out.println(stream.count()); // 取得数据个数
	}
}
```

取得了`Stream`类对象就可以进行数据的加工操作。

**范例：** 取消重复数据
* Stream类里面提供有一个消除重复的方法：`public Stream<T> distinct()`；
* 收集器：`public <R, A> R collect(Collector<? super T, A, R> collector)`；
  * `Collectors`类：`public static <T> Collector<T, ?, List<T>> toList()`；

```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("Hello");
		all.add("Hello");
		all.add("World");
		all.add("World");
		all.add("World");
		all.add("Demo");
		all.add("Demo");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		//System.out.println(stream.distinct().count()); // 取得数据个数
		// 取出掉所有的重复数据后形成新的数据集合，里面不包含重复的内容
		List<String> newAll = stream.distinct().collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

`Stream`类是进行数据处理的，那么必然要涉及到数据的筛选（过滤），`Stream`里面支持有数据的筛选操作：`public Stream<T> filter(Predicate<? super T> predicate)`。

**范例：** 数据过滤
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		List<String> newAll = stream.distinct().filter((x) -> x.contains("a")).collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

整个数据操作已经实现了过滤功能，但是发现这个时候的过滤是区分大小写的。那么就要在过滤之前对数据进行一些额外的处理，例如：统一将大写转为小写。在`Stream`类里面有专门的数据处理方法：`pulbic <R> Stream<R> map(Function<? super T, ? extends R> mapper)`。

**范例：** 数据处理后过滤
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		List<String> newAll = stream.distinct().map((x) -> x.toLowerCase()).filter((x) -> x.contains("a")).collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

在`Stream`接口里面提供有进行集合数据分页的操作：
* 设置跳过的数据行数：`public Stream<T> skip(long n)`；
* 设置取出的数据个数：`public Stream<T> limit(long maxSize)`；

```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		List<String> newAll = stream.distinct().map((x) -> x.toLowerCase()).skip(2).limit(2)
				.collect(Collectors.toList());
		newAll.forEach(System.out::println);
	}
}
```

在`Stream`接口里面还可以进行数据的全匹配或部分匹配：

* 全匹配：`public boolean allMatch(Predicate<? super T> predicate)`；
* 匹配任意一个：`public boolean anyMatch(Predicate<? super T> predicate)`；

**范例：** 实现数据的匹配查询

```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		if (stream.anyMatch((x) -> x.contains("Linux"))) {
			System.out.println("数据存在");
		}
	}
}
```

在实际之中有可能会出现多个匹配的条件，在断言型的函数式接口里面提供有如下的方法：

* 或操作：`default Predicate<T> or(Predicate<? super T> other)`；
* 与操作：`default Predicate<T> and(Predicate<? super T> other)`；

**范例：** 设置多个条件
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<String> all = new ArrayList<String>();
		all.add("IOS");
		all.add("Windows");
		all.add("Linux");
		all.add("mac OS");
		all.add("Android");
		all.add("unix");
		Predicate<String> p1 = (x) -> x.contains("Linux");
		Predicate<String> p2 = (x) -> x.contains("Windows");
		Stream<String> stream = all.stream(); // 取得Stream类对象
		if (stream.anyMatch(p1.or(p2))) { // 同时使用两个条件
			System.out.println("数据存在");
		}
	}
}
```

利用这样的匹配条件，可以针对于数据进行方便的查询操作。

### MapReduce基础模型

如果要想更好的反映出`Stream`的操作优势，必须结合`MapReduce`一起观察。

* 数据分析方法：`public Optional<T> reduce(BinaryOperator<T> accumulator)`；

**范例：** 实现一个`MapReduce`
```java
class ShopCar {
	private String pname; // 商品名称
	private double price; // 商品单价
	private int amount; // 购买数量
	public ShopCar(String pname, double price, int amount) {
		this.pname = pname;
		this.price  = price;
		this.amount = amount;
	}
	public String getPname() {
		return pname;
	}
	public double getPrice() {
		return price;
	}
	public int getAmount() {
		return amount;
	}
}
```

此类设计的时候专门设计出了商品的单价与数量，这样如果要想取得某一个商品的花费就必须使用数量乘以单价。

**范例：** 进行数据的保存于初步的处理
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<ShopCar> all = new ArrayList<ShopCar>();
		all.add(new ShopCar("宾利	", 8000.0, 10));
		all.add(new ShopCar("宝马", 4500.0, 35));
		all.add(new ShopCar("布加迪威龙", 12000.0, 8));
		all.stream().map((x) -> x.getAmount() * x.getPrice()).forEach(System.out::println);
	}
}
```

此时已经对每一个数据进行了处理。但是这个时候没有总价。于是要对处理后的数据进行统计操作，使用`reduce()`完成。

**范例：** 统计处理数据
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<ShopCar> all = new ArrayList<ShopCar>();
		all.add(new ShopCar("宾利	", 8000.0, 10));
		all.add(new ShopCar("宝马", 4500.0, 35));
		all.add(new ShopCar("布加迪威龙", 12000.0, 8));
		double s = all.stream().map((x) -> x.getAmount() * x.getPrice()).reduce((sum, m) -> sum + m).get();
		System.out.println("花费总金额：" + s);
	}
}
```

以上只是实现了一个最简单的`MapReduce`，但是所完成的统计功能实在是过于有限，如果要想实现更能完善的统计操作，需要使用`Stream`接口里面定义的一下方法：

* 按照`Double`处理：`public DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper)`；
* 按照`Integer`处理：`public IntStream mapToInt(ToIntFunction<? super T> mapper)`；
* 按照`Long`处理：`public LongStream mapToLong(ToLongFunction<? super T> mapper)`；

**范例：** 实现数据的统计操作
```java
public class TestDemo {
	public static void main(String[] args) throws Exception {
		List<ShopCar> all = new ArrayList<ShopCar>();
		all.add(new ShopCar("宾利	", 8000.0, 10));
		all.add(new ShopCar("宝马", 4500.0, 35));
		all.add(new ShopCar("布加迪威龙", 12000.0, 8));
		DoubleSummaryStatistics dss = all.stream().mapToDouble((sc) -> sc.getAmount() * sc.getPrice()).summaryStatistics();
		System.out.println("商品个数：" + dss.getCount());
		System.out.println("平均花费：" + dss.getAverage());
		System.out.println("最高花费：" + dss.getMax());
		System.out.println("最低花费：" + dss.getMin());
		System.out.println("总花费：" + dss.getSum());
	}
}
```

这种代码是在是过于麻烦，但是他的确是简化了代码的编写。
