## 动态代理

代理设计模式是在程序开发之中使用最多的设计模式，代理设计模式的核心是有真实业务实现类与代理业务实现类，并且代理类要完成比真实业务更多的处理操作。

### 传统代理设计模式的弊端

所有的代理设计模式如果按照设计要求来讲，必须是基于接口的设计，也就是说需要首先定义出核心接口的组成，下面模拟一个消息发送的代理操作结构。

**范例：**传统代理设计
```java
public class Application {
    public static void main(String[] args) throws Exception {
        IMessage message = new MessageProxy(new MessageReal());
        message.send();
    }
}
interface IMessage {
    void send();
}
class MessageReal implements IMessage {
    @Override
    public void send() {
        System.out.println("[SEND] Hello World!");        
    }
}
class MessageProxy implements IMessage {
    private IMessage message;
    public MessageProxy(IMessage message) {
        this.message = message;
    }
    @Override
    public void send() {
        if (this.connect()) {
            this.message.send();
            this.close();
        }
    }
    public boolean connect() {
        System.out.println("[INFO] open connection...");
        return true;
    }
    public void close() {
        System.out.println("[INFO] close connection.");
    }
}
```

以上的操作代码是一个标准的代理设计，但是会发现其中有着极强的耦合。以上的代理模式称为静态代理，一个代理类只为一个接口服务，即代理类与接口间产生了强耦合。

### 动态代理模式

通过静态代理的缺陷可以发现，最好的做法是为所有功能一直的业务操作接口提供统一的代理处理操作，而这就要通过动态代理来实现，但是在动态代理的过程中需要考虑到如下的一些问题：
* 不管是动态代理类还是静态代理类都一定要接收真实业务实现子类对象；
* 由于动态代理类不再与某一个具体的接口进行捆绑，所以应该可以动态获取类的接口信息；

在进行动态代理的实际操作中，首先需要关注的是一个接口，`InvocationHandler`这个接口定义了代理方法的执行。
```java
public interface InvocationHandler {
    /**
    * 代理方法调用，代理主体类里面执行的方法最终都是此方法
    * @param proxy 要代理的对象
    * @param method 要执行的接口方法
    * @param args 接口方法传递的参数
    * @return 某个方法的返回值
    * @throws Throwable 在调用过程中产生的错误
    */
    public Object invoke(Object proxy, Method method, Object[] args)
            throws Throwable;
}
```

由于在进行动态代理设计的时候对于动态对象的创建是由JVM底层完成的，此时主要依靠的是`java.lang.reflect.Proxy`程序类，而这个程序类之中只提供有一个核心方法：

* 代理对象：`public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h);`
  * `ClassLoader loader`：获取当前真实主题类的ClassLoader；
  * `Class<?>[] interfaces`：代理是围绕着接口进行的，所以一定要获取真实主体类的接口信息；
  * `InvocationHandler h`：代理处理的方法；

**范例：**实现动态代理机制
```java
public class Application {
    public static void main(String[] args) throws Exception {
        IMessage message = (IMessage) new DynamicProxy().bind(new MessageReal());
        message.send();
    }
}
class DynamicProxy implements InvocationHandler {
    private Object target;
    public Object bind(Object target) {
        this.target = target;
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), 
                target.getClass().getInterfaces(), this);
    }
    public boolean connect() {
        System.out.println("[INFO] open connection...");
        return true;
    }
    public void close() {
        System.out.println("[INFO] close connection.");
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("[EXEC] " + method);
        Object result = null;
        if (this.connect()) {
            method.invoke(this.target, args);
            this.close();
        }
        return result;
    }
}
interface IMessage {
    void send();
}
class MessageReal implements IMessage {
    @Override
    public void send() {
        System.out.println("[SEND] Hello World!");        
    }
}
```

在执行`Proxy.newProxyInstance()`过程中，该方法调用了大量的底层机制来进行代理对象的创建，所有的代理类是复合所有相关功能希求的操作功能类，它不再表示具体的接口，这样在处理的时候就必须依赖于类加载器与接口进行代理对象的伪造。
