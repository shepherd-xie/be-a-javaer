# Java 16

- **Pattern Matching for instanceof**
- **Records**
- Packaging Tool
- Elastic Metaspace
- ZGC: Concurrent Thread-Stack Processing
- UNIX-Domain Socket Channels
- Strongly Encapsulate JDK Internals by Default
- Warnings for Value-Based Classes
- Vector API (Incubator)
- Foreign Linker API (Incubator)
- Foreign-Memory Access API (Third Incubator)
- Sealed Classes (Second Preview)
- Enable C++14 Language Features (in the JDK source code)

## Sealed Classes

```java
public abstract sealed class Student
    permits ZhangSan, LiSi, ZhaoLiu {
    ...
        
}
```

