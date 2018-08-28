## Java7新特性

* [AutoCloseable](/chapter-2/section-29.md#AutoCloseable)
* [Try-with-resources](/chapter-2/section-29.md#Try-with-resources)

### AutoCloseable

AutoCloseable主要是用于资源操作时，实现资源的自动关闭。例如在进行数据库访问时，数据库的链接有限，在操作完成后要及时关闭，此时就可以使用AutoCloseable进行自动关闭。

为了方便说明，下面实现一个简单的消息发送。

**范例：**消息发送
```java
interface IMessage {
    void send();
}
class NetMessage implements IMessage {
    private String msg;
    public NetMessage(String msg) {
        this.msg = msg;
    }
    public void open() {
        System.out.println("[ OPEN] ==> 获取连接资源");
    }
    @Override
    public void send() {
        System.out.println("[ SEND] ==> " + this.msg);
    }
    public void close() {
        System.out.println("[CLOSE] ==> 关闭资源");
    }
}
public class Application {
    public static void main(String[] args) {
        NetMessage nm = new NetMessage("Hello World");
        nm.open();
        nm.send();
        nm.close();
    }
}
```

在每次使用完资源是都需要对资源链接进行关闭操作，此时能否将这一行为变为自动的？在JDK1.7版本中，退出了AutoCloseable接口，用于自动关闭。
* 关闭方法：void close() throws Exception;

AutoCloseable的自动关闭功能要结合Try-with-resources处理使用。

### Try-with-resources

try-with-resources语句是一个声明一个或多个资源的`try`语句。一个资源作为一个对象，必须在程序结束之后随之关闭。try-with-resources语句确保在语句的最后每个资源都被关闭。任何实现了`java.lang.AutoCloseable`的对象, 包括所有实现了`java.io.Closeable`的对象, 都可以用作一个资源。

**范例：**消息发送
```java
interface IMessage {
    void send();
}
class NetMessage implements IMessage, AutoCloseable {
    private String msg;
    public NetMessage(String msg) {
        this.msg = msg;
    }
    public void open() {
        System.out.println("[ OPEN] ==> 获取连接资源");
    }
    @Override
    public void send() {
        System.out.println("[ SEND] ==> " + this.msg);
    }
    @Override
    public void close() throws Exception {
        System.out.println("[CLOSE] ==> 关闭资源");
    }
}
public class Application {
    public static void main(String[] args) {
        try (NetMessage nm = new NetMessage("Hello World")) {
            nm.open();
            nm.send();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
