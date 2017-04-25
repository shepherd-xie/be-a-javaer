## String类的常用方法

---

String在所有的开发之中都一定要使用到，String类里面提供了一系列的功能操作方法，下面列举出一部分的方法，而对于有一些操作，需要等待全部的知识掌握时候才可以进行学习。

对于系统类的方法，一定要自己去查询文档，对于String类由于开发的过程中使用较多，所以一定要熟记String类中所提供的方法。

对于每一个文档的内容而言，他都有以下的几部分组成：

* 类的定义以及相关的继承结构；
* 类的一些简短的说明；
* 类中的成员组成；
* 类中所提供的构造方法；
* 类中所提供的的普通方法；
* 成员、构造方法、普通方法的详细说明。

### 字符与字符串

很多的语言之中都是利用了字符数组的概念来描述字符串的信息，这一点在String类的方法上也都有所提供。

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public String\(char\[\] value\) | 构造 | 将字符数组变为String类对象 |
| 2 | public String\(char\[\] value,int offset,int count\) | 构造 | 将部分字符数组变为String |
| 3 | public char charAt\(int index\) | 普通 | 返回指定索引对应的字符信息 |
| 4 | public char\[\] toCharArray\(\) | 普通 | 将字符串以字符数组的形式返回 |

**范例：**取出指定索引的字符

```java
public class MainClass {
    public static void main(String[] args) {
        String str = "hello";
        char c = str.charAt(0);
        System.out.println(c);
    }
}
```

程序之中字符串的下标都是从0开始的。

**范例：**字符数组与字符串的转换

```java
public class MainClass {
    public static void main(String[] args) {
        String str = "hello";
        char[] data = str.toCharArray();
        for (int i = 0; i < data.length; i ++) {
            System.out.println(data[i]);
        }
    }
}
```

**范例：**将字符串转大写

```java
public class MainClass {
    public static void main(String[] args) {
        String str = "hello";
        char[] data = str.toCharArray();
        for (int i = 0; i < data.length; i ++) {
            data[i] += 'A' - 'a';
        }
        System.out.println(new String(data));
        System.out.println(new String(data, 1, 3));
    }
}
```

**范例：**给定一个字符串，要求判断其是否由数组组成

**思路：**如果整个字符串要判断是不是数字无法实现，但是可以将字符串变为字符数组，而后判断每一个字符的内容是否是数字，如果该字符的范围在（'0'~'9'）指定的范畴之内，那么就是数字。

```java
public class MainClass {
    public static void main(String[] args) {
        String str = "681356486";
        if (isNumber(str)) {
            System.out.println("字符串由数字组成!");
        } else {
            System.out.println("字符串不是由数字组成");
        }
    }
    //判断字符串是否由数字组成
    public static boolean isNumber(String temp) {
        char[] data = temp.toCharArray();
        for (int i = 0; i < data.length; i ++) {
            if (data[i] > '9' || data[i] < '0') {
                return false;
            }
        }
        return true;
    }
}
```

如果写的某一个方法返回的内容是boolean，那么习惯性的做法是将其以“isXxx”进行命名。

### 字节与字符串

字节使用byte描述，使用字节一般主要用于数据的传输或者进行编码转换的时候使用，而咋String类里面就提供有将字符串变为字节数组的操作，目的就是为了传输以及编码转换。

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public String\(byte\[\] bytes\) | 构造 | 将将全部字节数组变为字符串 |
| 2 | public String\(byte\[\] bytes,int offset,int length\) | 构造 | 将部分字节数组变为字符串 |
| 3 | public byte\[\] getBytes\(\) | 普通 | 将字符串将字符串变为字节数组 |
| 4 | public byte\[\] getBytes\(String charsetName\) throws UnsupportedEncodingException | 普通 | 进行编码转换 |



