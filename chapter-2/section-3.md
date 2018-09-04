## 数组的定义与使用

---

在所有的开发之中，数组是一个非常重要的概念，所有项目都使用数组，但是它使用的数组与本次讲解的内容是有出入的，后面会更换数组的使用形式，但是其基本概念不会被改变。

### 数组的基本概念

数组指的就是一组相关变量的集合。例如：如果说现在想要定义100个整形变量，如果按照传统的思路，可能这样定义：int i1,i2,...i100;,一共写100个变量。

以上的形式的确可以满足技术要求，但是这里有一个，这100多个变量没有任何的逻辑的控制关系，完全独立，就会出现对象不方便管理的情况。那么在这种情况下就可以利用数组来解决此类问题，而数组本身也属于引用数据类型，

所以数组的定义语法如下：

* 声明并开辟数组：数据类型 数组名称\[\] = new 数据类型\[长度\];（或书写为此类形式：数据类型\[\] 数组名称 =new 数据类型\[长度\];）
* 分步完成：
  * 声明数组：数据类型 数组名称\[\] = null;
  * 开辟数组：数据名称 = new 数据类型\[长度\];

当定义数组后，可以采用“数组名称\[下标\|索引\]”的形式进行访问，但是所有数组的下标都是从0开始的，即：如果是3个长度的数组，那么下标的范围：0~2（0,1,2一共是三个）。如果访问的时候超过了数组的允许下标的长度，那么会出现数组越界异常（ArrayIndexOutOfBoundsException）。

以上给出的数组定义结构使用的是动态初始化的方式，即：数组会首先开辟内存空间，但是数组中的内容都是其对应数据的默认值。

由于数组是一种顺序的结构，并且数组的长度都是固定的，那么可以使用循环的方式输出，很明显需要知道for循环，而且在Java里面为了方便数组的输出提供有一个“数组名称.length”的属性，可以取得数组长度。

**范例：** 定义数组

```java
public class MainClass {
    public static void main(String[] args) {
        //声明并开辟了一个3个长度的数组
        int date[] = new int[3]; 
        date[0] = 10;
        date[1] = 20;
        date[2] = 30;
        for (int i = 0; i < date.length; i ++) {
            System.out.println(date[i]);
        }
    }
}
```

虽然数组的操作比较简单，但是其最麻烦的问题在于，它也属于引用数据类型，所以以上的代码依然需要牵扯到内存分配，与对象保存唯一的区别在于：对象中的堆内存保存的是属性，而数据中的堆内存保存的是一组信息。

![](/images/chapter-2/section-3/1.png)

以上使用的是第一种数组定义的语法，那么下面更换第二种；

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = null;    //声明数组    
        date = new int[3];     //开辟空间
        date[0] = 10;
        date[1] = 20;
        date[2] = 30;
        for (int i = 0; i < date.length; i ++) {
            System.out.println(date[i]);
        }
    }
}
```

下面还是以内存关系来描述以上的关系。

![](/images/chapter-2/section-3/2.png)

既然数组属于引用数据类型，那么数组一定可以发生引用传递。

**范例：** 数组的引用传递

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int[3];     
        date[0] = 10;
        date[1] = 20;
        date[2] = 30;
        int temp[] = date;
        temp[0] = 99;
        for (int i = 0; i < date.length; i ++) {
            System.out.println(date[i]);
        }
    }
}
```

![](/images/chapter-2/section-3/3.png)

以上数组的定义格式严格来讲是属于动态初始化，它的操作特点：先开辟数组空间，而后为数组中的的内容进行赋值。那么在数组定义之中还提供有静态初始化的操作，即：数组定义的同时就设置好了相应的数据内容，格式如下：

* 格式一：简化格式：数据类型 数组名称 \[\] ={值, 值, ...};

* 格式二：完整格式：数据类型 数组名称 \[\] = new 数据类型 \[\] {值, 值, ...};

**范例：** 数组的静态初始化

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int [] {1, 2, 3, 4, 5};
        for (int i = 0; i < date.length; i ++) {
            System.out.println(date[i]);
        }
    }
}
```

在实际的工作之中，数组是会存在的，但是它的内容大部分情况下都是通过传递的数据而动态生成的，很少会出现这种先开辟数组而后去使用的情况。

虽然数组支持顺序的数据访问操作，但是数组有一个最大的缺点 —— 长度不能够被改变，所以正因为如此，在开发之中，不会直接去应用数组，但是会使用到数组的概念。

### 二维数组（了解）

在之前的数组里面只有一个“\[\]”，所以此类数组就是一个普通的数组，或者再麻烦一点可以把它称为一维数组，如果现在想要描述更多的数据，可以使用二维数组，后面有两个“\[\]”。

一维数组严格来讲就是一行，类似于如下的形式：

| 索引 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 数据 | 90 | 80 | 70 | 60 | 50 | 40 | 30 | 20 |

如果在一维数组里面找到一个数据，只需要确定一个索引就够了。

二维数组就是一张数据表，有行有列，类似于如下的形式：

| 索引 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 0 | 90 | 80 | 70 | 60 | 50 | 40 | 30 | 20 |
| 1 | 91 | 81 | 71 | 61 | 51 | 41 | 31 | 21 |
| 2 | 92 | 82 | 72 | 62 | 52 | 42 | 32 | 22 |

如果现在要想在二维数组里面确定一个数据，需要行和列一起定位，例如：数字71的索引位置，行1列2“\[1\]\[2\]”。而对于二维数组的定义语法有如下两类：

* 动态初始化：数据类型 数组名称\[\]\[\] = new 数据类型\[行的个数\]\[列的个数\];
* 静态初始化：数据类型 数组名称\[\]\[\] = new 数据类型\[\]\[\] { { 值 , 值 , ... } , { 值 , 值 , ... } , ... } ;

通过定义结构可以发现，所谓的二维数组实际上就是将多个一维数组变为了一个大的数组并且为每一个一维数组设置了一个行号而已。

**范例：** 观察二维数组

```java
public class MainClass {
    public static void main(String[] args) {
        int date[][] = new int[][] {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        //外层循环是控制数组的数据行内容
        for (int i = 0; i < date.length; i ++) {
            //内层循环是控制数组的数据列内容
            for (int j = 0; j < date[i].length; j ++) {
                System.out.print(date[i][j] + "\t");
            }
            System.out.println();
        }
    }
}
```

随着开发的发展，在直接编写的代码里面很少会再去出现二维数组的概念。

### 数组与方法参数的传递（难点）

在之前所编写的所有方法传递数据几乎都使用的基本数据类型，那么除了基本数据类型之外，也可以传递数组，但是如果传递的是数组，请千万要记住观察内存分配图。

**范例：** 一个数组传递的程序

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int[]{1, 2, 3};
        change(date);    //int temp[] = date;
        for (int i = 0; i < date.length; i ++) {
            System.out.println(date[i]);
        }
    }
    //此方法定义在主类中，由主方法直接调用
    public static void change(int temp[]) {
        for (int i = 0; i < temp.length; i ++) {
            temp[i] *= 2;    //将数组的内容乘2
        }
    }
}
```

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int[]{1, 2, 3};
        int temp[] = date;
        for (int i = 0; i < temp.length; i ++) {
            temp[i] *= 2;    //将数组的内容乘2
        }
        for (int i = 0; i < date.length; i ++) {
            System.out.println(date[i]);
        }
    }
}
```

![](/images/chapter-2/section-3/4.png)

在进行数组的引用传递的过程之中，方法对数组的修改一定会影响到原始数据。

**范例：** 实现一个数组排序

数组的排序操作再笔试之中经常被问到，下面给出（升序）排序的基本原理：

* 原始数据：    2、1、9、0、5、3、7、6、8；
* 第一次排序：1、2、0、5、3、7、6、8、9；
* 第二次排序：1、0、2、3、5、6、7、8、9；
* 第三次排序：0、1、2、3、5、6、7、8、9。

以上只是给出了排序的基础原理过程，但是会根据数据的不同会出现不同的排序次数，但是不管有多少条数据，总的排序次数不会超过数组的长度。所以只要排序的次数达到了“长度\*长度”，那么所有的数据一定可以排序成功。

基础的实现：

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int[]{2, 1, 9, 0, 5, 3, 7, 6, 8};
        print(date);
        //外层控制排序的次数
        for (int i = 0; i < date.length; i ++) {
            //内层控制每次的排序控制
            for (int j = 0; j < date.length - 1; j ++) {
                if (date[j] > date[j + 1]) {
                    int t = date[j + 1];
                    date[j + 1] = date[j];
                    date[j] = t;
                }
            }
        }
        print(date);
    }
    //专门定义一个输出的方法
    public static void print(int temp[]) {
        for (int i = 0; i < temp.length; i ++) {
            System.out.print(temp[i] + "、");
        }
        System.out.println();
    }
}
```

**改善设计：**主方法设计上是作为程序的起点存在，那么所有的程序起点都可以称为客户端，既然是客户端，所有的代码编写一定要简单，那么可以采用方法进行封装。

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int[] { 2, 1, 9, 0, 5, 3, 7, 6, 8 };
        sort(date);//实现排序
        print(date);
    }
    // 这个方法专门负责排序
    public static void sort(int arr[]) {
        // 外层控制排序的次数
        for (int i = 0; i < arr.length; i++) {
            // 内层控制每次的排序控制
            for (int j = 0; j < arr.length - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    int t = arr[j + 1];
                    arr[j + 1] = arr[j];
                    arr[j] = t;
                }
            }
        }
    }
    // 专门定义一个输出的方法
    public static void print(int temp[]) {
        for (int i = 0; i < temp.length; i++) {
            System.out.print(temp[i] + "、");
        }
        System.out.println();
    }
}
```

**面试题：**请编写一个数组排序操作

答案如上。

**范例：** 实现数组的转置

如果要想实现转置的操作，有两个思路：

* 定义一个新的数组，而后将原始数组按照倒序的方式插入到新的数组之中，随后改变原始数组引用。

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int[] { 1, 2, 3, 4, 5, 6, 7, 8 };
        //首先定义一个新的数组，长度与原始数组一致
        int temp[] = new int[date.length];
        int foot = date.length - 1;    //控制date数组的索引
        //对于新的数组按照索引由小到大的顺序循环
        for (int i = 0; i < temp.length; i ++) {
            temp[i] = date[foot];
            foot --;
        }
        //此时temp的内容就是date转置后的结果
        //让date指向temp,而date的原始数据就成为垃圾了
        date = temp;
        print(date);
    }
    // 专门定义一个输出的方法
    public static void print(int temp[]) {
        for (int i = 0; i < temp.length; i++) {
            System.out.print(temp[i] + "、");
        }
        System.out.println();
    }
}
```

虽然以上的代码实现了转置的操作，但是遗憾的是，代码里面会产生垃圾。

* 利用算法，在一个数组上完成转置操作：
  * 原始数组：    1、2、3、4、5、6、7、8，转置次数：数组长度 ÷ 2
  * 第一次转置：8、2、3、4、5、6、7、1
  * 第二次转置：8、7、3、4、5、6、2、1
  * 第三次转置：8、7、6、4、5、3、2、1
  * 第四次转置：8、7、6、5、4、3、2、1

但是以上给出的数组长度是一个偶数，如果是一个奇数呢？

* 原始数组：    1、2、3、4、5、6、7，转置次数：数组长度 ÷ 2
* 第一次转置：7、2、3、4、5、6、1
* 第二次转置：7、6、3、4、5、2、1
* 第三次转置：7、6、5、4、3、2、1

也就是说不管是奇数个数还是偶数个数的数组，转置的次数计算方式完全一样。

```java
public class MainClass {
    public static void main(String[] args) {
        int date[] = new int[] { 1, 2, 3, 4, 5, 6, 7, 8 };
        reverse(date);    //实现转置
        print(date);
    }
    //此方法实现数组的转置操作
    public static void reverse(int arr[]) {
        int len = arr.length / 2;    //转置的次数
        int head = 0;    //头部索引
        int tail = arr.length - 1;    //尾部索引
        for (int i = 0; i < len; i ++){
            int t = arr[head];
            arr[head] = arr[tail];
            arr[tail] = t;
            head ++;
            tail --;
        }
    }
    // 专门定义一个输出的方法
    public static void print(int temp[]) {
        for (int i = 0; i < temp.length; i++) {
            System.out.print(temp[i] + "、");
        }
        System.out.println();
    }
}
```

以上实现的是一个一维数组的转置，那么如果是二维数组呢？

**前提：**是一个行与列完全相同的数组；

1    2    3

4    5    6

7    8    9

第一次转置：

1    4    3

2    5    6

7    8    9

第二次转置：

1    4    7

2    5    6

3    8    9

第三次转置：

1    4    7

2    5    8

3    6    9

```java
public class MainClass {
    public static void main(String[] args) {
        int date[][] = new int[][] { 
            { 1, 2, 3 }, 
            { 4, 5, 6 }, 
            { 7, 8, 9 } 
            };
        reverse(date); // 实现转置
        print(date);
    }
    // 此方法实现数组的转置操作
    public static void reverse(int arr[][]) {
        for (int i = 0; i < arr.length; i ++) {
            for (int j = i; j < arr[i].length; j ++) {
                if (i != j) {
                    int t = arr[i][j];
                    arr[i][j] = arr[j][i];
                    arr[j][i] = t;
                }
            }
        }
    }
    // 专门定义一个输出的方法
    public static void print(int temp[][]) {
        for (int i = 0; i < temp.length; i++) {
            for (int j = 0; j < temp[i].length; j ++) {
                System.out.print(temp[i][j] + "、");
            }
            System.out.println();
        }
        System.out.println();
    }
}
```

以上给出的是一个等行列的数组，但如果行和列不一样呢？

1    2    3

4    5    6

开辟一个新的数组，数组的行数是原数组的列数，列数是原数组的行数。

需要改变原始数组的引用，会产生垃圾。

以上实现了方法接收数组的操作情况，同样方法也可以返回数组。

**范例：** 方法返回数组

```java
public class MainClass {
    public static void main(String[] args) {
        int data[] = init();    //接受数组
        print(data);
    }
    public static int[] init() {
        return new int[] { 1, 2, 3 };
    }
    // 专门定义一个输出的方法
    public static void print(int temp[]) {
        for (int i = 0; i < temp.length; i++) {
            System.out.print(temp[i] + "、");
        }
        System.out.println();
    }
}
```

重点关注方法的返回值即可。

### 数组操作方法

Java本身针对于数组是有提供类库支持的，下面来说两个与数组有关的操作方法：

1、数组拷贝：可以将一个数组的部分内容拷贝到另一个数组中；

* 语法：System.arraycopy\(源数组名称, 源数组拷贝开始索引, 目标数组名称, 目标数组拷贝开始索引, 长度\);

**范例：** 实现数组拷贝

* 数组A：1、2、3、4、5、6、7、8
* 数组B：11、22、33、44、55、66、77、88
* 要求拷贝后的数组B：11、22、5、6、7、66、77、88

```java
public class MainClass {
    public static void main(String[] args) {
        int dataA[] = new int[] { 1, 2, 3, 4, 5, 6, 7, 8 };
        int dataB[] = new int[] { 11, 22, 33, 44, 55, 66, 77, 88 };
        System.arraycopy(dataA, 4, dataB, 2, 3);
        print(dataB);
    }
    public static void print(int temp[]) {
        for (int i = 0; i < temp.length; i++) {
            System.out.print(temp[i] + "、");
        }
        System.out.println();
    }
}
```

2、数组排序：之前给出了排序的基本操作，但是在开发里面如果要进行排序只需要使用如下代码即可：

* 语法：java.util.Arrays.sort\(数组名称\);

**范例：** 实现排序

```java
 public class MainClass {
    public static void main(String[] args) {
        int data[] = new int[] { 3, 6, 1, 2, 8, 0 };
        java.util.Arrays.sort(data);
        print(data);
    }
    public static void print(int temp[]) {
        for (int i = 0; i < temp.length; i++) {
            System.out.print(temp[i] + "、");
        }
        System.out.println();
    }
}
```

### 对象数组（重点）

数组是引用类型，而类也同样是引用类型，所以如果是对象数组的话表示一个引用类型中嵌套有其他引用类型。

在之前使用的数组都属于基本数据类型的数组，但是所有的引用数据类型也同样可以定义数组，这样的数组称为对象数组。如果想要定义对象数组（以类为例），可以才用如下的形式完成：

* 动态初始化：开辟之后对象数组的内容都是null值
  * 声明并开辟数组：类名称 对象数组名称\[\] = new 类名称\[长度\];
  * 分步完成：
    * 声明对象数组：类名称 对象数组名称\[\] = null;
    * 开辟对象数组：对象数组名称\[\] = new 类名称\[长度\];
* 静态初始化：类名称 对象数组名称\[\] = new 类名称\[\]{实例化对象, 实例化对象, ...};

**范例：** 对象数组的动态初始化

```java
class Book {
    private String title;
    private double price;
    public Book(String t, double p) {
        title = t;
        price = p;
    }
    //setter、getter、无参构造略
    public String getInfo() {
        return "书名：" + title + "，价格：" + price;
    }
}
public class MainClass {
    public static void main(String[] args) {
        //开辟了一个3个长度的对象数组
        Book books[] = new Book[3];
        //对象数组中的每个数据都需要分别实例化
        books[0] = new Book("Java基础入门", 89.8);
        books[1] = new Book("操作系统", 51.5);
        books[2] = new Book("数据结构", 100.2);
        for (int i = 0; i < books.length; i ++) {
            System.out.println(books[i].getInfo());
        }
    }
}
```

对象数组实际上就是将多个对象交给数组统一管理。

**范例：** 使用静态初始化

```java
class Book {
    private String title;
    private double price;
    public Book(String t, double p) {
        title = t;
        price = p;
    }
    //setter、getter、无参构造略
    public String getInfo() {
        return "书名：" + title + "，价格：" + price;
    }
}
public class MainClass {
    public static void main(String[] args) {
        //开辟了一个3个长度的对象数组
        Book books[] = new Book[]{
            new Book("Java基础入门", 89.8),
            new Book("操作系统", 51.5),
            new Book("数据结构", 100.2)
        };
        for (int i = 0; i < books.length; i ++) {
            System.out.println(books[i].getInfo());
        }
    }
}
```

一般而言，使用对象数组的时候只会定义成一维数组。

#### 总结

1、数组用的很少，但是一定会用，而且数组相关的逻辑关系比较麻烦；

2、一切以一维数组为主，要明白数组的定义语法与内存关系（对象一致）；

3、对象数组的定义语法，对象数组 = 多个对象；

4、数组有一个最大的天生短板：长度固定，所以这就限制了数组在开发中的出现；

5、数组的排序：java.util.Arrays.sort\(数组名称\)。

