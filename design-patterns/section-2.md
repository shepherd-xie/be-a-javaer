# 单例模式(Singleton)

**单例模式**是一种创建型设计模式，让你能够保证一个类只有一个实例，并提供一个访问该实例的全局节点。

![单例模式结构](https://images.orkva.com/images/2023/02/21/structure-zh.png)

1. 饿汉式

```java
public class Singleton {
    // 声明实例变量
    private static final Singleton INSTANCE = new Singleton();

    // 私有构造方法
    private Singleton() {}

    // 对外提供访问
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

2. 饿汉式 静态代码块

```java
public class Singleton {
    // 声明变量
    private static final Singleton INSTANCE;
    
    static {
        // 静态代码块实例化变量
        INSTANCE = new Singleton();
    }

    // 私有构造方法
    private Singleton() {}

    // 对外提供访问
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

3. 懒汉式

```java
public class Singleton {
    // 声明变量
    private static volatile Singleton INSTANCE;

    // 私有构造方法
    private Singleton() {}

    // 对外提供访问
    public static Singleton getInstance() {
        if (INSTANCE == null) {
            synchronized (Singleton.class) {
                if (INSTANCE == null) {
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
```

4. 懒汉式 静态内部类

```java
public class Singleton {

    // 私有构造方法
    private Singleton() {}

    private static final class InstanceHolder {
        // 声明实例变量
        private static final Singleton INSTANCE = new Singleton();
    }

    // 对外提供访问
    public static Singleton getInstance() {
        return InstanceHolder.INSTANCE;
    }
}
```

5. 枚举实现

```java
public enum Singleton {
    INSTANCE;
}
```
