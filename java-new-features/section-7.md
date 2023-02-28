# Java 12

- Switch 表达式
- JMH 基准测试
- JVM 常量 API
- G1的可中断 mixed GC
- G1归还不使用的内存

## Switch 表达式

```java
int number = switch(something) {
    case APPLE -> 2;
    case ORANGE -> 4;
    case PEAR -> 5;
}
```

