# Java 14

- instanceof 的模式匹配
- switch 表达式
- 文本块
- record
- NullPointerException 错误推断
- JVM 新特性

## instanceof 的模式匹配

```java
Object o = new String("hello");
if (o instanceof String s) {
    s.append("world");
}
```

## record

类似于 Lombok 的 @Data 注解，将类做进一步的细分。

```java
public record Person(String name, int age) {
    
}
```

