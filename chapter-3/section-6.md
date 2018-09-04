## 数字操作类

* [Math类](/chapter-3/section-6.md#math类)
* [Random类](/chapter-3/section-6.md#random类)
* [大数字操作类](/chapter-3/section-6.md#大数字操作类)

### Math类

Math就是一个专门进行数学计算的操作类，里面提供了一系列的数学计算方法。

在Math类里面提供的一切方法都是static型的方法，因为Math类里面没有普通属性。

Math类中有这样的一个方法：
* 四舍五入：public static long round(double a)。

**范例：** 观察四舍五入
```java
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		System.out.println(Math.round(15.5)); // 16
		System.out.println(Math.round(-15.5)); // -15
		System.out.println(Math.round(-15.51)); // -16
	}
}
```

如果进行负数四舍五入的时候，操作的数据小数位大于0.5才进位，小于等于0.5不进位。

### Random类

这个类的主要功能是取得随机数的操作类。

**范例：** 产生10个不大于100的正整数（0~99）

```java
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Random random = new Random();
		for (int i = 0; i < 10; i ++) {
			System.out.print(random.nextInt(100) + "、");
		}
	}
}
```

既然Random可以产生对技术，下面就希望利用其来实现一个36选7的功能。

**范例：** 36选7
```java
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		Random random = new Random();
		int[] date = new int[7]; // 开辟一个7个元素的数组
		int foot = 0; // 此为数组操作交表
		while (foot < 7) { // 不知道多少次循环可以保存完数据，所以使用while循环
			int t = random.nextInt(37); // 生成一个不大于37的随机数
			if (!isRepeat(date, t)) { // 重复
				date[foot ++] = t; // 保存数据
			}
		}
		Arrays.sort(date);
		for (int i = 0; i < date.length; i ++) {
			System.out.print(date[i] + ", ");
		}
	}
	/**
	 * 此方法主要是判断是否存在有重复的内容，但是不允许保存0
	 * @param temp 指的是已经保存的数据
	 * @param num 新生成的数据
	 * @return 如果成功的保存了，那么返回true，否者返回false
	 */
	public static boolean isRepeat(int[] temp, int num) {
		if (num == 0) { // 没有必要向下继续判断
			return true;
		}
		for (int i = 0; i < temp.length; i ++) {
			if (temp[i] == num) {
				return true;
			}
		}
		return false;
	}
}
```

在很多的开发之中随机数都一定会有。

### 大数字操作类

如果说现在要操作的数字值很大，那么首先想到的应该是double，那么如果说现在计算的结果超过了double该如何接收这个数据。
```java
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		System.out.println(Double.MAX_VALUE * Double.MAX_VALUE); // Infinity
	}
}
```

现在发现此时的计算结果并不存在，因为已经超过了double的范畴。

**面试题：**请问当前假设有两个很大的数字进行数学计算（超过double范围），你给怎么做？

如果真的超过了double的范围，那么肯定无法用double进行保存，只有String才可以准确的保存好这个数据。如果真的需要对很大的数据进行计算，只能够将其变为String型，而后按位取出每一个字符保存的数据，进行手工的计算。

所以在Java里面考虑到了此类情况，专门提供了大数字的操作类，其中就有BigInteger、BigDecimal两种。

**BigInteger**

BigInteger类的构造方法：public BigInteger(String val)，它接受的是String型。

```java
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		BigInteger biga = new BigInteger("894165498148168684618");
		BigInteger bigb = new BigInteger("48154687351354698415");
		System.out.println("加法操作：" + biga.add(bigb));
		System.out.println("减法操作：" + biga.subtract(bigb));
		System.out.println("乘法操作：" + biga.multiply(bigb));
		System.out.println("除法操作：" + biga.divide(bigb));
		// 数组里面只有两个元素：第一个元素表示的是商，第二个元素表示的是余数
		BigInteger[] result = biga.divideAndRemainder(bigb);
		System.out.println("商：" + result[0] + "，余数：" + result[1]);
	}
}
```

在Java里面虽然提供了大数字的操作类，但是很多的时候，我们对于数字的精度要求可能会更高，所以Java本身提供的数字类实际意义并不大。

**BigDecimal两种**

BigInteger不能保存小数，而BigDecimal可以保存小数。在BigDecimal类里面提供有如下的构造：

* 构造一：public BigDecimal(String val)；
* 构造二：public BigDecimal(double val)。

与BigInteger一样，BigDecimal本身也支持基础的数学计算。可是使用BigDecimal还有一个非常重要的目的：就是可以利用它来实现准确的四舍五入操作。

之前使用过Math.round()实现过四舍五入操作，但是这种操作有一个问题，所有的小数位都四舍五入了。遗憾的是BigDecimal类里面没有直接提供有四舍五入的操作支持，可以利用除法计算实现：

* 除法操作：public BigDecimal divide(BigDecimal divisor, int scale, int roundingMode)
  * BigDecimal divisor：被除数；
  * int scale：保留的小数位；
  * int roundingMode：进位模式（public static final int ROUND_HALF_UP）。

**范例：** 实现准确的四舍五入
```java
class MyMath {
	/**
	 * 实现准确位数的四舍五入操作
	 * @param num 要进行四舍五入的数字
	 * @param scale 要保留的小数位
	 * @return 处理后的四舍五入数据
	 */
	public static double round(double num, int scale) {
		BigDecimal biga = new BigDecimal(num);
		BigDecimal bigb = new BigDecimal(1);
		return biga.divide(bigb, scale, BigDecimal.ROUND_HALF_UP).doubleValue();
	}
}
public class MainClass{ 
	public static void main(String[] args) throws Exception {
		System.out.println(MyMath.round(19.79469494, 2));
		System.out.println(MyMath.round(-15.5, 0));
		System.out.println(MyMath.round(15.5, 0));
	}
}
```

#### 总结

1. Math类重点要清楚round()方法的机制；
2. Random类生成随机数；
3. 如果数据量大就使用BigInteger或BigDecimal，这两个类是Number的子类。


