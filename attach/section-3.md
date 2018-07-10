## 单例设计模式优化

---

* 使用同步保证线程安全synchronized
* 使用volatile关键字
  * volatile提醒编译器它后面所定义的变量随时都有可能改变，因此编译后的程序每次需要存储或读取这个变量的时候，都会直接从变量地址中读取数据。如果没有volition关键字，则编译器可能优化读取和存储，可能暂时使用寄存器中的值，如果这个变量由别的程序更新了的话，将出现不一致的现象。
* 防止反射调用私有构造方法
* 让单例类序列化安全

```java
/**
 * 单例模式
 * 1. 多线程访问的安全问题
 * 2. 加上volatile关键字保证变量的一致性
 * 3. 防止反射调用私有构造方法
 * 4. 让单例类可以被序列化
 */
public class Singleton implements Serializable {
    private volatile static Singleton INSTANCE = null;
    private Singleton() {
        if (INSTANCE != null) {
            throw new RuntimeException("Singleton that has been instantiated.");
        }
    }
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