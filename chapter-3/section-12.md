## 反射与Annotation

从JDK1.5之后Java开发提供了Annotation技术支持，这种技术为项目的编写带来新的模型，而后经过十多年的发展，Annotation技术得到了非常广泛的应用，并且已经在所有的项目之中都会存在。

### 获取Annotation信息

在进行类或方法定义的时候都可以使用一系列的Annotation进行声明，于是如果要想获取这些Annotation的信息，那么就可以直接通过反射来完成。在 `java.lang.reflect` 里面有一个 `AccessibleObject` 类，在本类中提供有获取Annotation类的方法：
* 获取全部Annotation： `public Annotation[] getAnnotations()`；
* 获取指定Annotation： `public <T extends Annotation> T getAnnotation(Class<T> annotationClass)`；

**范例：** 定义一个接口，并且在接口上使用Annotation
```java
public class Application {
    public static void main(String[] args) throws Exception {
        {
            // 获取接口上全部的Annotation信息
            Annotation[] annotations = IMessage.class.getAnnotations();
            for (Annotation annotation : annotations) {
                System.out.println(annotation);
            }
        }
        System.out.println("=========================================");
        {
            // 获取MessageImpl子类上的Annotation
            Annotation[] annotations = MessageImpl.class.getAnnotations();
            for (Annotation annotation : annotations) {
                System.out.println(annotation);
            }
        }
        System.out.println("=========================================");
        {
            // 获取MessageImpl.send()方法上的Annotation
            Method method = MessageImpl.class.getDeclaredMethod("send", String.class);
            Annotation[] annotations = method.getAnnotations();
            for (Annotation annotation : annotations) {
                System.out.println(annotation);
            }
        }
    }
}
@FunctionalInterface
@Deprecated(since = "1.0")
interface IMessage {
    void send(String msg);
}
@SuppressWarnings("serial")
class MessageImpl implements IMessage, Serializable {
    @Override
    public void send(String msg) {
        System.out.println("[SEND] " + msg);
    }
}
```

不同的Annotation有它的存在范围，下面对比两个Annotation：
* `FunctionalInterface`
```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface {}
```
* `SuppressWarnings`
```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {}
```

此时可以发现 `FunctionalInterface` 是在运行时生效的Annotation，所以在程序执行的时候可以获取此Annotation，而 `SuppressWarnings` 是在源代码编写时有效。表示生效范围的 `RetentionPolicy` 共有三种状态： 

```java
public enum RetentionPolicy {
    /**
     * Annotations are to be discarded by the compiler.
     */
    SOURCE,
    /**
     * Annotations are to be recorded in the class file by the compiler
     * but need not be retained by the VM at run time.  This is the default
     * behavior.
     */
    CLASS,
    /**
     * Annotations are to be recorded in the class file by the compiler and
     * retained by the VM at run time, so they may be read reflectively.
     *
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME
}
```

### 自定义Annotation

现在已经清楚了Annotation的获取以及Annotation的运行策略，但是最为关键的是如何自定义Annotation。为此Java里面提供了新的语法，使用 `@interface` 声明Annotation。

**范例：** 自定义Annotation
```java
public class Application {
    public static void main(String[] args) throws Exception {
        Method method = Message.class.getDeclaredMethod("send", String.class);
        CustomizeAnnotation annotation = method.getAnnotation(CustomizeAnnotation.class);
        String msg = annotation.title() + " [" + annotation.url() + "]";
        method.invoke(Message.class.getDeclaredConstructor().newInstance(), msg);
    }
}
@Retention(RetentionPolicy.RUNTIME)
@interface CustomizeAnnotation {
    String title();

    String url() default "www.hello.com";
}
class Message {
    @CustomizeAnnotation(title = "Hello")
    public void send(String msg) {
        System.out.println("[SEND] " + msg);
    }
}
```

使用Annotation之后的最大的特点是可以结合反射机制实现程序的处理。

### 工厂设计模式与Annotation整合

```java
public class Application {
    public static void main(String[] args) throws Exception {
        MessageService messageService = new MessageService();
        messageService.send("Hello World!");
    }
}
@Retention(RetentionPolicy.RUNTIME)
@interface UseMessage {
    Class<?> clazz();
}
@UseMessage(clazz = NetMessageImpl.class)
class MessageService {
    private IMessage message;
    public MessageService() {
        UseMessage annotation = MessageService.class.getAnnotation(UseMessage.class);
        this.message = (IMessage) Factory.getInstance(annotation.clazz());
    }
    public void send(String msg) {
        this.message.send(msg);
    }
}
class Factory {
    private Factory() {}
    public static <T> T getInstance(Class<T> clazz) {
        try {
            return (T) new MessageProxy().bind(clazz.getDeclaredConstructor().newInstance());
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
class MessageProxy implements InvocationHandler {
    private Object target;
    public Object bind(Object target) {
        this.target = target;
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), 
                target.getClass().getInterfaces(), this);
    }
    public boolean connect() {
        System.out.println("[PROXY INFO] Channel connection...");
        return true;
    }
    public void close() {
        System.out.println("[PROXY INFO] Channel closed.");
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        try {
            if (this.connect()) {
                return method.invoke(target, args);
            } else {
                throw new Exception("[ERROR] Unable to open channel.");
            }
        } finally {
            this.close();
        }
    }
}
interface IMessage {
    void send(String msg);
}
class MessageImpl implements IMessage {
    @Override
    public void send(String msg) {
        System.out.println("[SEND] " + msg);
    }
}
class NetMessageImpl implements IMessage {
    @Override
    public void send(String msg) {
        System.out.println("[NET SEND] " + msg);        
    }
}
```
