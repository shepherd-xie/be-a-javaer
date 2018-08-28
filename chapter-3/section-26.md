## Map集合

Collection每一次都只会保存一个对象，而Map主要是负责保存一对对象的信息。

如果说现在要保存一对关联数据（key=value）的时候，那么如果直接使用Collection就不能直接满足要求，可以使用Map接口实现此类数据的保存，并且Map接口还提供有根据key查找value的功能。

在开发之中，`Collection`集合保存数据的目的是为了输出，`Map`集合保存数据的目的是为了进行`key`的查找。

### Map接口简介

Map接口是进行二元对象保存的最大父接口，该接口定义如下：
```java
public interface Map<K, V>
```

该接口为一个独立的父接口，并且在进行接口对象实例化的时候需要设置Key与Value的类型，也就是说在整体操作的时候需要保存两个内容，在Map接口里面定义有如下的常用方法。

| No. | 方法名称 | 类型 | 描述 |
| :---: | :---: | :---: | :---: |
| 1 | public V put(K key, V value) | 普通 | 向集合中保存数据 |
| 2 | public V get(Object key) | 普通 | 根据key查找对应的value数据 |
| 3 | public Set<Map.Entry<K, V>> entrySet() | 普通 | 将Map集合转化为Set集合 |
| 4 | public boolean containsKey(Object key) | 普通 | 查询指定的key是否存在 |
| 5 | public Set<K> keySet() | 普通 | 取出全部的key |
| 6 | public V remove(Object key) | 普通 | 根据key删除内容 |

**范例：**观察Map的特点
```java
package com.alpha.demo;
import java.util.HashMap;
import java.util.Map;
public class TestDemo { 
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = Map.of("one", 1, "two", 2, "one", 101, null, 0);
        System.out.println(map);
	}
}
```

在Map集合之中数据的保存就是按照`key = value`的形式存储的，并且使用`of()`方法操作的时候里面是不允许重复的，如果出现重复则会出现`IllegalArgumentException`，如果设置的内容为`null`，则会出现`NullPointerException`。

对于现在使用的`of()`方法严格意义上来讲并不是Map集合的标准用法，因为正常的开发之中需要通过Map集合的子类来进行接口对象的实例化，常用的子类：`HashMap`，`Hashtable`，`TreeMap`，`linkedHashMap`，`ConcurrentHashMap`。

### HashMap

`HashMap`是`Map`接口之中最为常见的一个子类，该类的主要特点是无序存储，`HashMap`类的定义如下：
```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {}
```

![](/images/chapter-3/Collection/HashMap.png)

**范例：**观察`Map`集合的使用
```java
public class Application {
    public static void main(String[] args) throws Exception {
        Map<String, Integer> map = new HashMap<>();
        map.put("one", 1);
        map.put("two", 2);
        map.put("one", 101);
        map.put(null, 0);
        map.put("zero", null);
        System.out.println(map.get("one"));
        System.out.println(map.get(null));
        System.out.println(map.get("ten"));
    }
}
```

以上的操作形式为`Map`集合使用的标准形式，通过代码可以发现，通过`HashMap`实例化的`Map`接口可以针对与`key`或`value`保存`null`的数据，同时即使保存相同的`key`值也并不会报错，只是将`value`旧的内容使用新内容替换了。

但是对于`put()`方法本身是提供返回值的，这个返回值就是在`key`相同的情况下返回替换掉的旧的`value`。

**范例：**观察`put()`方法
```java
public class Application {
    public static void main(String[] args) throws Exception {
        Map<String, Integer> map = new HashMap<>();
        System.out.println(map.put("one", 1));
        System.out.println(map.put("one", 101));
    }
}
```

`HashMap`在今后的项目开发之中是一定会使用到的，为了深入理解`HashMap`的底层机制，下面对其源码进行解析：
```java
public class HashMap<K,V> extends AbstractMap<K,V>
        implements Map<K,V>, Cloneable, Serializable {

    /**
     * 默认初始容量 - 必须是2的幂
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    /**
     * 最大容量。如果具有参数的任一构造函数隐式指定较高值，则使用最大容量。
     */
    static final int MAXIMUM_CAPACITY = 1 << 30;

    /**
     * 在构造函数中未指定时使用的加载因子。
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    /**
     * 链表-树 转换阈值
     */
    static final int TREEIFY_THRESHOLD = 8;

    /**
     * 在调整大小期间 树-链表 转换阈值
     */
    static final int UNTREEIFY_THRESHOLD = 6;

    /**
     * 最小树容量
     */
    static final int MIN_TREEIFY_CAPACITY = 64;

    /**
     * 基础的内部维护节点，单向链表
     */
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
        // 全参构造、getter/setter、toString、hashCode、equals 略
    }

    /**
     * 内部维护的二元数组，其大小始终为 2 的幂数
     */
    transient Node<K,V>[] table;

    /**
     * 用作缓存的entrySet
     */
    transient Set<Map.Entry<K,V>> entrySet;

    /**
     * key-value映射的数量
     */
    transient int size;

    /**
     * 此HashMap经过结构修改的次数结构修改是指更改HashMap中映射数或以其他方式修改其内部结构（例如，重新散列）的修改。
     */
    transient int modCount;

    /**
     * 下一个要调整大小的值（capacity * load factor）。
     */
    int threshold;

    /**
     * 加载因子
     *
     * @serial
     */
    final float loadFactor;

    /**
     * 使用指定的初始容量和加载因子构造一个空的HashMap。
     *
     * @param  initialCapacity 初始容量
     * @param  loadFactor      加载因子
     * @throws IllegalArgumentException 如果初始容量为负或加载因子为非正数
     */
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                    initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                    loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }

    /**
     * 使用指定的初始容量和默认加载因子（0.75）构造一个空的HashMap。
     *
     * @param  initialCapacity 初始容量
     * @throws IllegalArgumentException 初始容量为负
     */
    public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }

    /**
     * 使用默认初始容量（16）和默认加载因子（0.75）构造一个空的HashMap。
     */
    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }

    /**
     * 调用putVal方法实现添加操作
     */
    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    /**
     * Implements Map.put and related methods.
     *
     * @param hash hash for key
     * @param key the key
     * @param value the value to put
     * @param onlyIfAbsent if true, don't change existing value
     * @param evict if false, the table is in creation mode.
     * @return previous value, or null if none
     */
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        // 如果在内部维护的tab[hash]为空时，直接将新节点存放到该位置
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        // 不为空则进行链表或树的拓展
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        // 如果容量大于转换阈值，将其转换为树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        // 如果容量大于下一个要调整大小的值，扩容至原大小的二倍并且重新散列
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }

    /**
     * 当链表长度过长时将节点转变为红黑树以提高效率
     * @param <K>
     * @param <V>
     */
    static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K, V> parent; 
        TreeNode<K, V> left;
        TreeNode<K, V> right;
        TreeNode<K, V> prev;
        boolean red;
    }
}
```

### LinkedHashMap

`HashMap`本身存储数据是无序的，如果希望添加的元素有序，则可以使用`LinkedHashMap`：
```java
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
```

![](/images/chapter-3/Collection/LinkedHashMap.png)

由于`LinkedHashMap`是基于链表的存储，所以在进行操作时数据量不易过大，否则容易造成时间复杂度攀升。

**范例：**使用`LinkedHashMap`
```java
public class Application {
    public static void main(String[] args) throws Exception {
        Map<String, Integer> map = new LinkedHashMap<>();
        map.put("one", 1);
        map.put("two", 2);
        map.put("one", 101);
        map.put(null, 0);
        map.put("zero", null);
        System.out.println(map);
    }
}
```

### Hashtable

在`Map`接口下还有一个`Hashtable`的子类，此类是在JDK 1.0的时候提供的，属于最早的`Map`集合的实现操作，在JDK 1.2的时候让其多实现了一个`Map`接口，从而保存下来继续使用。
```java
public class Hashtable<K,V>
    extends Dictionary<K,V>
    implements Map<K,V>, Cloneable, java.io.Serializable
```

![](/images/chapter-3/Collection/Hashtable.png)

**范例：**使用Hashtable
```java
public class Application { 
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = new Hashtable<String, Integer>();
		map.put("壹", 1);
		map.put("贰", 2);
		map.put("叁", 3);
		map.put(null, 0); // key的内容为null
		System.out.println(map);
	}
}
```

发现Hashtable里面对于key和value的数据都不允许设置为null。

> 程序抛出的异常代码如下：
> 
> ```java
> Exception in thread "main" java.lang.NullPointerException
> 	at java.util.Hashtable.put(Hashtable.java:464)
> 	at com.alpha.demo.TestDemo.main(TestDemo.java:10)
> ```
> 
> 查阅JDK源码：
> 
> ```java
> public synchronized V put(K key, V value) {
>     if (value == null) {
>         throw new NullPointerException();
>     }
>     Entry<?,?> tab[] = table;
>     int hash = key.hashCode();
>     int index = (hash & 0x7FFFFFFF) % tab.length;
>     Entry<K,V> entry = (Entry<K,V>)tab[index];
>     for(; entry != null ; entry = entry.next) {
>         if ((entry.hash == hash) && entry.key.equals(key)) {
>             V old = entry.value;
>             entry.value = value;
>             return old;
>         }
>     }
>     addEntry(hash, key, value, index);
>     return null;
> }
> ```

**面试题：**请解释HashMap与Hashtable的区别？

| No. | 区别 | HashMap（90%） | Hashtable（10%） |
| :---: | :---: | :---: | :---: |
| 1 | 推出时间 | JDK 1.2时推出，属于新的类 | JDK 1.0时推出，属于旧的类 |
| 2 | 性能 | 采用异步处理 | 采用同步处理 |
| 3 | 数据安全 | 非线程安全 | 线程安全 |
| 4 | 设置null | 允许key或value内容为null | 不允许设置null |

### TreeMap

`TreeMap`属于有序的`Map`集合类型，他可以按照`key`进行排除，所以在使用`TreeMap`时一定要配合`Comparable`接口使用。
```java
public class TreeMap<K,V>
    extends AbstractMap<K,V>
    implements NavigableMap<K,V>, Cloneable, java.io.Serializable
```

![](/images/chapter-3/Collection/TreeMap.png)

**范例：**使用`TreeMap`实现排序
```java
public class Application {
    public static void main(String[] args) throws Exception {
        Map<String, Integer> map = new TreeMap<>();
        map.put("C", 3);
        map.put("B", 2);
        map.put("A", 1);
        System.out.println(map);
    }
}
```

在使用`TreeMap`时可以根据`key`实现数据的排序操作，由于要调用`Comparable`接口的`compareTo()`方法进行排序，所以`key`是不允许为`null`的，`value`则允许为`null`。

### Map.Entry接口

之前对`HashMap`进行源码分析是发现其内部是将键值对保存在了`HashMap.Node`之中，而`HashMap.Node`本身实现的是`Map.Entry`接口。
```java
static class Node<K,V> implements Map.Entry<K,V>
```

所以可以得出：所有的`key-value`都被封装在`Map.Entry`接口之中，`Map.Entry`接口的定义如下：
```java
public interface Map<K, V> {
    interface Entry<K, V> {}
}
```

在JDK1.9之后`Map`提供了一个静态方法可以直接构造`Map.Entry`：
* 创建`Map.Entry`对象：`static <K,V> Map.Entry<K,V> entry​(K k, V v)`；

**范例：**创建`Map.Entry`对象
```java
public class Application {
    public static void main(String[] args) throws Exception {
        Map.Entry<String, Integer> entry = Map.entry("one", 1);
        System.out.println("获取key：" + entry.getKey());
        System.out.println("获取value：" + entry.getValue());
        System.out.println(entry.getClass().getName());
    }
}
```

可以发现，`Map.Entry`的主要作用就是作为一个`key`和`value`的包装类型使用，而大部分情况下在进行数据存储的时候都会将`key`和`value`包装为一个`Map.Entry`对象进行使用。

### 使用Iterator输出Map集合

在之前强调过，只要是集合的输出一定要使用Iterator完成，但是在整个Map接口里面并没有定义任何的可以返回Iterator接口对象的方法，所以下面如果要想使用Iterator输出Map集合，首先必须要针对于Map集合与Collection集合保存数据的特点进行分析后才能实现。

每当用户使用put()方法向Map集合里面保存一对数据的时候，实际上所有的数据都会被自动的封装为Map.Entry接口对象。观察Map.Entry接口定义：

```java
public static interface Map.Entry<K,V>
```

在这个接口里面定义了两个操作：
* 取得key：public K getKey()；
* 取得value：public V getValue()；

![](/images/chapter-3/section-26/1.png)

在Map接口里面定义有一个将Map集合转化为Set集合的方法：public Set<Map.Entry<K, V>> entrySet()。

_**Map集合利用Iterator接口输出的步骤：**_
* _**利用Map接口的entrySet()方法将Map集合变为Set集合，里面的泛型是Map.Entry；**_
* _**利用Set集合中的iterator()方法将Set集合进行Iterator输出；**_
* _**每一次Iterator循环取出的都是Map.Entry接口对象，利用此对象进行key与value的取出。**_

**范例：**利用Iterator实现Map接口的输出
```java
public class Application {
	public static void main(String[] args) throws Exception {
		Map<String, Integer> map = new Hashtable<String, Integer>();
		map.put("壹", 1);
		map.put("贰", 2);
		map.put("叁", 3);
		// 将Map集合变为Set集合，目的是为了使用 iterator()方法
		Set<Map.Entry<String, Integer>> set = map.entrySet();
		Iterator<Map.Entry<String, Integer>> iter = set.iterator();
		while (iter.hasNext()) {
			Map.Entry<String, Integer> me = iter.next();
			System.out.println(me.getKey() + " = " + me.getValue());
		}
	}
}
```

以上的代码会不定期的出现，一定要掌握步骤。

### 关于Map集合中key的说明

在使用Map接口的时候可以发现，几乎可以使用任意的类型来作为key或value的存在，那么也就表示可以使用自定义的类型作为key。作为key的自定义的类必须要覆写Object类中的hashCode()与equals()两个方法。

在以后使用Map集合的时候，首选的key类型是String，尽量不要去使用其他类型。
