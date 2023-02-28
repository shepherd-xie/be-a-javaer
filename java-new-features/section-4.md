# Java 9

- 模块化系统
- jshell
- 私有接口方法
- 改进的 Stream API
- 改进 try-with-resources
- 响应式流（Reactive Streams) API

## 模块化系统

Java 9 最大的变化之一是引入了模块系统（Jigsaw 项目）。

模块就是代码和数据的封装体。模块的代码被组织成多个包，每个包中包含Java类和接口；模块的数据则包括资源文件和其他静态信息。

Java 9 模块的重要特征是在其工件（artifact）的根目录中包含了一个描述模块的 module-info.class 文 件。 工件的格式可以是传统的 JAR 文件或是 Java 9 新增的 JMOD 文件。这个文件由根目录中的源代码文件 module-info.java 编译而来。该模块声明文件可以描述模块的不同特征。

```java
module com.orkva.module.provider {
    // 声明可以使用的包路径
    exports com.orkva.external;
}
```

```java
module com.orkva.module.consumer {
    // 引用需要的包路径
    requires com.orkva.external;
}
```

## 响应式流（Reactive Streams) API

响应式流 API 主要由以下四个接口构成：

- Flow.Publisher
- Flow.Subscriber
- Flow.Subscription
- Flow.Processor

```java
public class MySubscribe implements Flow.Subscriber<String> {
    private Flow.Subscription subscription;

    @Override
    public void onSubscribe(Flow.Subscription subscription) {
        System.out.println("on Subscribe:" + subscription);
        this.subscription = subscription;
        this.subscription.request(2);
    }

    @Override
    public void onNext(String item) {
        System.out.println("item:" + item);
        this.subscription.request(1);
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("on error:" + throwable);
    }

    @Override
    public void onComplete() {
        System.out.println("on complete");
    }
}
```

```java
public class MyPublisher {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(1);
        SubmissionPublisher<String> submissionPublisher = new SubmissionPublisher<>(executor, Flow.defaultBufferSize());
        submissionPublisher.subscribe(new MySubscribe());
        submissionPublisher.submit("data 1");
        submissionPublisher.submit("data 2");
        submissionPublisher.submit("data 3");
    }
}
```

